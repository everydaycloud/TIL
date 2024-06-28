V0 is the original API setup that we don't use when creating new routes.

## V0 

In V0 blueprints reflect the table structure, so a get would get everything from a specific table,
but it wouldn't join it with another one, so we'd have to send two requests and combine tha data
on the frontend. 

## V1

This is the current setup. The blueprints here reflect the needs of the frontend, so we'll join
tables and we may not need to call more than one route on the frontend. 

## Other things to remember

GET requests - return the full resource
POST requests - return ONLY the id of the posted resource
PUT requests - return ONLY an empty JSON and the 200 code
tells us that the request was successful
