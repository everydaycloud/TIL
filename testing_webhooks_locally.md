# Using Ngrok to test the Postmark webhook locally

    https://ngrok.com/download
    
## 1
Download ngrok, follow the steps in the link and sign up for an
account to get an auth-token and follow the commands there to 
add it.

## 2
Once it's done, run this command in the terminal (make sure the
localhost portal matches the port on which your backend runs).

      ngrok http http://api.localhost:5050/ --host-header api.localhost:5050

## 3 
If testing a Postmark webhook, log in to Postmark, choose the
Development Server > Transactional Stream > Webhook > Add Webhook

In the URL input paste the ngrok url followed by the endpoint where
the information should be sent. e.g.

    https://3d7c-2a02-c7c-b085-6f00-b486-80d7-v7g5-66c6.ngrok-free.app/v1/postmark/
    (not a real url, but structure is the same)

Set up basic auth credentials, which should be hidden in the development.env file. 

Pick the events you want to receive info about and save the webhook.

Now you can set up your tests to use the webhook locally. E.g.

      response = await staff_authenticated_client.post(
        "/v1/postmark/",
        auth=("postmark", app.config["POSTMARK_HOOK_PASSWORD"]),
        json={"RecordType": "BadEmailAddress", "Email": patient_email, "ID": 1234},
        headers={"Content-Type": "application/json"},
        subdomain="api",
    )
    payload = await response.get_json()
    assert response.status_code == 200, payload

