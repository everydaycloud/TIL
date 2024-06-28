# Testing Requests locally

1. Get this VS Code extension https://marketplace.visualstudio.com/items?itemName=humao.rest-client

2. Create an .http file (e.g. test.http)

3. Add the request details and at the top of the file a little 'send request' button will appear.

    post http://api.localhost:5050/v1/postmark/
    Authorization: Basic cG9zdG1hcms6cG9zdG1hcms=
    Content-Type: application/json
    {
      "RecordType": "SoftBounce/Undeliverable",
      "Email": "patient@curaleafpharmacy.co.uk",
      "ID": 1234
    }
