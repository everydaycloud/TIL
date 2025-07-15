# Testing webhooks locally 


## Does the webhook endpoint live on a subdomain?
Like "api" in api.curaleafclinic.com/v1/stripe ?
If yes:

In the terminal type 

    sudo nano /etc/hosts

Then paste the ip of the backend server when it's runnning followed by
the subdomain.localhost.

The file will then look like this.
<img width="460" height="282" alt="Screenshot 2025-07-15 at 09 19 36" src="https://github.com/user-attachments/assets/617d0f64-0d2f-4509-b11e-e2542b53a6c5" />

When you save it, it'll require the computer password.

## Then

1. Log in to Stripe and switch to Sandbox

If you don't have access to Sandboxes in Stripe ask Phil for access as this is required
for this process. 

Once you see the sandbox dashboard, copy the secret key and paste it in development.env

    CURA_STRIPE_API_KEY="sk_test_7634598752689"

2. Install the Stripe CLI and log in
https://docs.stripe.com/stripe-cli

3. Start the webhook using the format
   <subdomain(optional)>.localhost:<port(that the backend runs on)>/<path to the webhook endpoint>

    stripe listen --forward-to api.localhost:5050/v1/stripe/

When you do this it will say: 

    > Ready! You are using Stripe API Version [2025-06-30.basil]. Your webhook signing secret is whsec_25817f9

Go to development.env again and paste the webhook secret in the correct place.

    CURA_STRIPE_ENDPOINT_SECRET="whsec_e7a4b"
    
In another terminal window start the backend where the webhook is located.

4. Set up a test patient in Stripe

Create a new patient in the Sandbox.
Use an email and patient name that's already in data.py as otherwise things will fail.
Add a fake payment method https://docs.stripe.com/testing#testing-interactively
Create a subscription they can sign up to and make sure that the price_id for it is 
added in development.env in a correct format. This will need to be reverted after.

    CURA_STRIPE_SUBSCRIPTION_PRICE_CL="10,price_1REQntLoy0gQywnT2Nl18Phm"

Sign the patient up to the subscription and watch the logs for the Stripe CLI,
the backend server. You can also check the logs in Stripe to see the full events
https://dashboard.stripe.com/test/workbench/webhooks
The test webhook will be in 'Local listeners' at the bottom of the page





