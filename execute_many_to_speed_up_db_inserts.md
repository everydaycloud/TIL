This was my code (in a migration) which ran fine on a local server, because quart_db runs execute_migrations
during the app's startup sequence (a before_serving hook). Quart fully awaits startup before 
the server binds to a port and starts accepting connections. So migrations finish, then 
anything connects. 

By the time a connection is ever requested, the "ready" Event is already set — wait_for(event.wait(), 6) returns instantly.
In tests, async with app.test_app() starts the app but quart_db fires migrations as a background task (asyncio.create_task)
rather than blocking startup. That lets test_app() yield immediately so pytest can continue setting up fixtures — but those 
fixtures then request a database connection while migrations are still running. quart_db sees "not done yet" and waits, 
with the 6-second ceiling.

This took longer than 6s because there are 6k+ lines in the csv and they were inserted one by one.

``` python
async def migrate(connection: Connection) -> None:

    class VerifiedGPPractice(BaseModel):
        email: str
        name: str
        address_lines: list[str]
        postcode: str
        ods_code: str | None = None
        phone_number: str | None = None
        alternative_emails: list[str]

        @field_validator("address_lines", "alternative_emails", mode="before")
        @classmethod
        def parse_json_array(cls, value: Any) -> list[str]:
            if not value or value.strip() == "null":
                return []
            return json.loads(value)

        @field_validator("ods_code", "phone_number", mode="before")
        @classmethod
        def empty_to_none(cls, value: Any) -> str | None:
            return value if value and value.strip() != "null" else None

    verified_gp_surgeries: list[VerifiedGPPractice] = []
    csv_path = os.path.join(os.path.dirname(__file__), "verified_gp_data.csv")
    with open(csv_path) as csvfile:
        reader = csv.DictReader(csvfile)
        for raw_row in reader:
            verified_row = VerifiedGPPractice.model_validate(dict(raw_row))
            verified_gp_surgeries.append(verified_row)

    async with connection.transaction():
        for verified_gp in verified_gp_surgeries:
            result = await insert_gp_practice(
                connection,
                address_lines=verified_gp.address_lines,
                alternative_emails=verified_gp.alternative_emails,
                email=verified_gp.email,
                name=verified_gp.name,
                ods_code=verified_gp.ods_code,
                phone_number=verified_gp.phone_number,
                postcode=verified_gp.postcode,
                verified=True,
            )
            assert result is not None

            await insert_gp_practice_history(
                connection,
                gp_practice_id=result.id,
                address_lines=result.address_lines,
                alternative_emails=result.alternative_emails,
                email=result.email,
                name=result.name,
                ods_code=result.ods_code,
                phone_number=result.phone_number,
                postcode=result.postcode,
                verified=result.verified,
                reason="initial verified insert",
                staff_id=None,
                partner_user_id=None,
            )

```

Why 6 seconds wasn't enough
Migration 349 loads GP practice seed data in a loop:

for verified_gp in verified_gp_surgeries:          # 8,689 rows
    result = await insert_gp_practice(...)          # 1 INSERT → wait → result
    await insert_gp_practice_history(...)           # 1 INSERT → wait → result
Each await is a full round-trip to PostgreSQL: send query, wait for network + db execution, receive result. Even on localhost that's ~0.3–0.5 ms per query. With 8,689 × 2 verified + 609 × 2 unverified = 18,596 round-trips, that's 5–9 seconds. Right on the knife-edge of the 6-second timeout, and consistently over it.


How the fix works
What execute_many does instead: It sends one prepared statement to PostgreSQL, 
then pipelines all 8,689 (or 609) parameter sets through that single prepared 
plan without separate round-trips per row. asyncpg handles list[str] → text[] 
encoding naturally per row.
Why UUID generation in Python: We need the same IDs for both the gp_practices 
and gp_practice_history inserts. With execute_many there's no RETURNING, so 
we pre-generate them with uuid.uuid4() and pass id explicitly in both inserts.


``` python 
class VerifiedGPPractice(BaseModel):
        email: str
        name: str
        address_lines: list[str]
        postcode: str
        ods_code: str | None = None
        phone_number: str | None = None
        alternative_emails: list[str]

        @field_validator("address_lines", "alternative_emails", mode="before")
        @classmethod
        def parse_json_array(cls, value: Any) -> list[str]:
            if not value or value.strip() == "null":
                return []
            return json.loads(value)

        @field_validator("ods_code", "phone_number", mode="before")
        @classmethod
        def empty_to_none(cls, value: Any) -> str | None:
            return value if value and value.strip() != "null" else None

    verified_gp_surgeries: list[VerifiedGPPractice] = []
    csv_path = os.path.join(os.path.dirname(__file__), "verified_gp_data.csv")
    with open(csv_path) as csvfile:
        reader = csv.DictReader(csvfile)
        for raw_row in reader:
            verified_row = VerifiedGPPractice.model_validate(dict(raw_row))
            verified_gp_surgeries.append(verified_row)

    verified_ids = [uuid.uuid4() for _ in verified_gp_surgeries]

    async with connection.transaction():
        await connection.execute_many(
            """INSERT INTO gp_practices
                   (id, address_lines, alternative_emails, email, name, ods_code, phone_number, postcode, verified)
               VALUES (:id, :address_lines, :alternative_emails, :email, :name, :ods_code, :phone_number, :postcode, TRUE)""",
            [
                {
                    "id": verified_ids[i],
                    "address_lines": gp.address_lines,
                    "alternative_emails": gp.alternative_emails,
                    "email": gp.email,
                    "name": gp.name,
                    "ods_code": gp.ods_code,
                    "phone_number": gp.phone_number,
                    "postcode": gp.postcode,
                }
                for i, gp in enumerate(verified_gp_surgeries)
            ],
        )
        await connection.execute_many(
            """INSERT INTO gp_practice_history
                   (gp_practice_id, address_lines, alternative_emails, email, name,
                    ods_code, phone_number, postcode, verified, reason)
               VALUES (:gp_practice_id, :address_lines, :alternative_emails, :email, :name,
                       :ods_code, :phone_number, :postcode, TRUE, 'initial verified insert')""",
            [
                {
                    "gp_practice_id": verified_ids[i],
                    "address_lines": gp.address_lines,
                    "alternative_emails": gp.alternative_emails,
                    "email": gp.email,
                    "name": gp.name,
                    "ods_code": gp.ods_code,
                    "phone_number": gp.phone_number,
                    "postcode": gp.postcode,
                }
                for i, gp in enumerate(verified_gp_surgeries)
            ],
        )
```
