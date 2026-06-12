* Export data from a table to a CSV file using the \copy command *

If you have access to a remote PostgreSQL database server,
but you don't have sufficient privileges to write to a file on it,
you can use the PostgreSQL built-in command \copy.

The \copy command runs the COPY statement behind the scenes.
However, instead of the server writing the CSV file, psql writes
the CSV file and transfers data from the server to your local file system.

To use \copy command, you need to have sufficient privileges to your local machine.
It does not require PostgreSQL superuser privileges.

For example:

``` sql
    \copy (
    SELECT DISTINCT ON (pgpd.email)
      pgpd.email AS gp_email,
      pgpd.name AS gp_name,
      pgpd.address_lines AS gp_address_lines,
      pgpd.postcode AS gp_postcode,
      pgpd.phone_number AS gp_phone_number,
      pnhsd.gp_ods_code AS ods_code
    FROM patient_gp_details pgpd
    LEFT JOIN patient_nhs_login_details pnhsd
    ON pgpd.patient_id = pnhsd.patient_id
    WHERE pgpd.email IS NOT NULL
    )
    TO '/Users/barbara/downloads/verified_gps.csv'
    WITH CSV
```
