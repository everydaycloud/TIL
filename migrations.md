# Migrations 

Migrations are basically changes to the database. There are two main ways to make
changes to a live databse. One is to write migration files manually. This means that 
we start out with migration file 0 and then every new change to the database is 
added by adding a new migration file on top 1, 2, 3 etc. 

Another way to do this is by making changes to the main setup file and using an ORM
to create the migration files automatically. In theory, these should then be reviewed
to make sure that they are correct, but this doesn't always happen. 

It's sometimes possible to downgrade to a previous version of the database, but this 
can create a lot of problems depending on the types of changes that were made before. 
It seems better to keep stacking new changes on top (this doesn't mean not deleting
or altering what is already there).
