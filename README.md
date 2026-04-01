Build AI WhatsApp Bots with Pure Python
This guide will walk you through the process of creating a WhatsApp bot using the Meta (formerly Facebook) Cloud API with pure Python, and Flask particular. We'll also integrate webhook events to receive messages in real-time and use OpenAI to generate AI responses. For more information on the structure of the Flask application, you can refer to this documentation.

Prerequisites
A Meta developer account — If you don’t have one, you can create a Meta developer account here.
A business app — If you don't have one, you can learn to create a business app here. If you don't see an option to create a business app, select Other > Next > Business.
Familiarity with Python to follow the tutorial.
Table of Contents
Build AI WhatsApp Bots with Pure Python
Prerequisites
Table of Contents
Get Started
Step 1: Select Phone Numbers
Step 2: Send Messages with the API
Step 3: Configure Webhooks to Receive Messages
Start your app
Launch ngrok
Integrate WhatsApp
Testing the Integration
Step 4: Understanding Webhook Security
Verification Requests
Validating Verification Requests
Validating Payloads
Step 5: Learn about the API and Build Your App
Step 6: Integrate AI into the Application
Step 7: Add a Phone Number
Datalumina
Tutorials
Get Started
Overview & Setup: Begin your journey here.
Locate Your Bots: Your bots can be found here.
WhatsApp API Documentation: Familiarize yourself with the official documentation.
Helpful Guide: Here's a Python-based guide for sending messages.
API Docs for Sending Messages: Check out this documentation.
Step 1: Select Phone Numbers
Make sure WhatsApp is added to your App.
You begin with a test number that you can use to send messages to up to 5 numbers.
Go to API Setup and locate the test number from which you will be sending messages.
Here, you can also add numbers to send messages to. Enter your own WhatsApp number.
You will receive a code on your phone via WhatsApp to verify your number.
Step 2: Send Messages with the API
Obtain a 24-hour access token from the API access section.
It will show an example of how to send messages using a curl command which can be send from the terminal or with a tool like Postman.
Let's convert that into a Python function with the request library.
Create a .env files based on example.env and update the required variables. Video example here.
You will receive a "Hello World" message (Expect a 60-120 second delay for the message).
Creating an access that works longer then 24 hours

Create a system user at the Meta Business account level.
On the System Users page, configure the assets for your System User, assigning your WhatsApp app with full control. Don't forget to click the Save Changes button.
See step 1 here
See step 2 here
Now click Generate new token and select the app, and then choose how long the access token will be valid. You can choose 60 days or never expire.
Select all the permissions, as I was running into errors when I only selected the WhatsApp ones.
Confirm and copy the access token.
Now we have to find the following information on the App Dashboard:

APP_ID: "<YOUR-WHATSAPP-BUSINESS-APP_ID>" (Found at App Dashboard)
APP_SECRET: "<YOUR-WHATSAPP-BUSINESS-APP_SECRET>" (Found at App Dashboard)
RECIPIENT_WAID: "" (This is your WhatsApp ID, i.e., phone number. Make sure it is added to the account as shown in the example test message.)
VERSION: "v18.0" (The latest version of the Meta Graph API)
ACCESS_TOKEN: "" (Created in the previous step)
You can only send a template type message as your first message to a user. That's why you have to send a reply first before we continue. Took me 2 hours to figure this out.

Step 3: Configure Webhooks to Receive Messages
Please note, this is the hardest part of this tutorial.

Start your app
Make you have a python installation or environment and install the requirements: pip install -r requirements.txt
Run your Flask app locally by executing run.py
Launch ngrok
The steps below are taken from the ngrok documentation.

You need a static ngrok domain because Meta validates your ngrok domain and certificate!

Once your app is running successfully on localhost, let's get it on the internet securely using ngrok!

If you're not an ngrok user yet, just sign up for ngrok for free.
Download the ngrok agent.
Go to the ngrok dashboard, click Your Authtoken, and copy your Authtoken.
Follow the instructions to authenticate your ngrok agent. You only have to do this once.
On the left menu, expand Cloud Edge and then click Domains.
On the Domains page, click + Create Domain or + New Domain. (here everyone can start with one free domain)
Start ngrok by running the following command in a terminal on your local desktop:
ngrok http 8000 --domain your-domain.ngrok-free.app
ngrok will display a URL where your localhost application is exposed to the internet (copy this URL for use with Meta).
Integrate WhatsApp
In the Meta App Dashboard, go to WhatsApp > Configuration, then click the Edit button.

In the Edit webhook's callback URL popup, enter the URL provided by the ngrok agent to expose your application to the internet in the Callback URL field, with /webhook at the end (i.e. https://myexample.ngrok-free.app/webhook).
Enter a verification token. This string is set up by you when you create your webhook endpoint. You can pick any string you like. Make sure to update this in your VERIFY_TOKEN environment variable.
After you add a webhook to WhatsApp, WhatsApp will submit a validation post request to your application through ngrok. Confirm your localhost app receives the validation get request and logs WEBHOOK_VERIFIED in the terminal.
Back to the Configuration page, click Manage.
On the Webhook fields popup, click Subscribe to the messages field. Tip: You can subscribe to multiple fields.
If your Flask app and ngrok are running, you can click on "Test" next to messages to test the subscription. You recieve a test message in upper case. If that is the case, your webhook is set up correctly.
Testing the Integration
Use the phone number associated to your WhatsApp product or use the test number you copied before.

Add this number to your WhatsApp app contacts and then send a message to this number.
Confirm your localhost app receives a message and logs both headers and body in the terminal.
Test if the bot replies back to you in upper case.
You have now succesfully integrated the bot! 🎉
Now it's time to acutally build cool things with this.
Step 4: Understanding Webhook Security
Below is some information from the Meta Webhooks API docs about verification and security. It is already implemented in the code, but you can reference it to get a better understanding of what's going on in security.py

Verification Requests
Source

Anytime you configure the Webhooks product in your App Dashboard, we'll send a GET request to your endpoint URL. Verification requests include the following query string parameters, appended to the end of your endpoint URL. They will look something like this:

GET https://www.your-clever-domain-name.com/webhook?
  hub.mode=subscribe&
  hub.challenge=1158201444&
  hub.verify_token=meatyhamhock
The verify_token, meatyhamhock in the case of this example, is a string that you can pick. It doesn't matter what it is as long as you store in the VERIFY_TOKEN environment variable.

Validating Verification Requests
Source

Whenever your endpoint receives a verification request, it must:

Verify that the hub.verify_token value matches the string you set in the Verify Token field when you configure the Webhooks product in your App Dashboard (you haven't set up this token string yet).
Respond with the hub.challenge value.
Validating Payloads
Source

WhatsApp signs all Event Notification payloads with a SHA256 signature and include the signature in the request's X-Hub-Signature-256 header, preceded with sha256=. You don't have to validate the payload, but you should.

To validate the payload:

Generate a SHA256 signature using the payload and your app's App Secret.
Compare your signature to the signature in the X-Hub-Signature-256 header (everything after sha256=). If the signatures match, the payload is genuine.
Step 5: Learn about the API and Build Your App
Review the developer documentation to learn how to build your app and start sending messages. See documentation.

Step 6: Integrate AI into the Application
Now that we have an end to end connection, we can make the bot a little more clever then just shouting at us in upper case. All you have to do is come up with your own generate_response() function in whatsapp_utils.py.

If you want a cookie cutter example to integrate the OpenAI Assistans API with a retrieval tool, then follow these steps.

Watch this video: OpenAI Assistants Tutorial
Create your own assistant with OpenAI and update your OPENAI_API_KEY and OPENAI_ASSISTANT_ID in the environment variables.
Provide your assistant with data and instructions
Update openai_service.py to your use case.
Import generate_reponse into whatsapp_utils.py
Update process_whatsapp_message() with the new generate_reponse() function.
Step 7: Add a Phone Number
When you’re ready to use your app for a production use case, you need to use your own phone number to send messages to your users.

To start sending messages to any WhatsApp number, add a phone number. To manage your account information and phone number, see the Overview page. and the WhatsApp docs.

If you want to use a number that is already being used in the WhatsApp customer or business app, you will have to fully migrate that number to the business platform. Once the number is migrated, you will lose access to the WhatsApp customer or business app. See Migrate Existing WhatsApp Number to a Business Account for information.

Once you have chosen your phone number, you have to add it to your WhatsApp Business Account. See Add a Phone Number.

When dealing with WhatsApp Business API and wanting to experiment without affecting your personal number, you have a few options:

Buy a New SIM Card
Virtual Phone Numbers
Dual SIM Phones
Use a Different Device
Temporary Number Services
Dedicated Devices for Development
Recommendation: If this is for a more prolonged or professional purpose, using a virtual phone number service or purchasing a new SIM card for a dedicated device is advisable. For quick tests, a temporary number might suffice, but always be cautious about security and privacy. Remember that once a number is associated with WhatsApp Business API, it cannot be used with regular WhatsApp on a device unless you deactivate it from the Business API and reverify it on the device.

Datalumina
This document is provided to you by Datalumina. We help data analysts, engineers, and scientists launch and scale a successful freelance business — $100k+ /year, fun projects, happy clients. If you want to learn more about what we do, you can visit our website and subscribe to our newsletter. Feel free to share this document with your data friends and colleagues.
