## SQL 'SELECT ... FOR UPDATE <options>'

## ANY WORKER THAT MUTATES SHOULD LOCK ROWS 

### FOR UPDATE WAIT

WAIT - no need to actually write this as it's the default. This
will wait until it can access a row and only then complete the operation.

Lock timeout - it should probably timeout after a certain time, but who
knows it could be waiting forever causing problems.

Workers don't time out, which then leads to a deadlock and that breaks
everything, because the worker will be stuck waiting.

## FOR UPDATE SKIP LOCKED

SKIP LOCKED - will skip the rows it can't access. This is what we use
in most places.

## FOR UPDATE NO WAIT

NO WAIT - will raise an exception if a row is inaccessible.


There is a query in Postgres that allows you to see active locks and
spot these problems.

## Always use async connection.transaction(): with locks

This is because they don't work properly otherwise. 

Inside a transaction a lock won't be released until the every action
within the transaction completes. Without a transaction the lock will
only stay in place as long as that one query exists and the other actions 
within the worker will be performed without the lock, which can cause
lots of problems.

## Quart DB query lock

Quart db doesn't allow more than opne query to use the connection at the
time. This is why connection.iterate() can't have other things going on
inside it. 
Connection.fetch_all() has to be used when we need to e.g. get a list of 
patients from postgres, but also add their names from Firestore to spit 
out a list of them that matches the correct model. This just won't work 
within iterate().

