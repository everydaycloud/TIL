# Async vs Sync and Requests

It's not so much whether individual functions are sync or asynchronous, 
but it's about what the framework (Flask, Quart etc) uses. 

The requests are always the same, because the servers translate your 
framework into the universal request syntax.

WSGI - sync 
ASGI - async 

hypercorn serves both 

