# Playground SQL

Playground SQL allows testing queries and getting the data from the database in a 
way that's more convenient than using the terminal. 

1. Create a playground.sql file and type in any commands that you need to test.

       SELECT * FROM customers;

2. Then in the terminal use the command

       psql -d <database-name> -f playground.sql > playground.txt

   This will create a .txt file in which you can see the query output.

Make sure that these files are gitignored.
