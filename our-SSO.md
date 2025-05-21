Ssh will remember your auth, so it will remember it’s you 

SSO every http request is stateless, so every request you make authenticates you every time

When we make a request to third parties we use an api key to do this, but when a user uses our service we can’t expect them to do that.

This used to be done with username and password sent with the request, but that means the password is exposed. That’s not a huge issue in itself, but it’s bad because people tend to duplicate their password across services. 

Scram hashing - new way of doing this??

You log in, you get the secret and then you send the secret on every request and this is how it works now.

We use Microsoft SSO for our login 

When someone logs in on our website, we send the user to Microsoft and it logs you in and sends you back with a jwt token, which we then check is valid. 
We take what the login returns and we store it in the browser and send with every request as a header. This secret is added to a cookie and it’s marked ad valid and that’s sent on every request as a default. 
We add a session id and secret to the cookie.
