Enum list values can be NOT NULL when the table is constructed. 
If the Enum is not set it'll just be an empty array.

    async def migrate(connection: Connection) -> None:

    await connection.execute(
        """CREATE TYPE EVIDENCE_TYPE_T
             AS ENUM ('NCRS', 'PATIENT_UPLOAD', 'GP_SCR')"""
    )

    await connection.execute(
        """CREATE TABLE patient_application_details (
                                         id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
                                 patient_id UUID REFERENCES patients(id) NOT NULL,
                             affiliate_code TEXT,
                    evidence_used_in_review EVIDENCE_TYPE_T[] NOT NULL,
                          how_hear_about_us TEXT,
                                is_transfer BOOLEAN DEFAULT FALSE,
                              transfer_from TEXT
             )
        """
    )


evidence_used_in_review will be an empty array if no value is inserted.
