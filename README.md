# Twilio Pay Demo

## Step 1: Create a Stripe Account / Login to Stripe
- https://dashboard.stripe.com/dashboard

## Step 2: Configure Stripe <Pay> Connector
- Programmable Voice Section > Pay Connectors
- Visit Voice Settings (https://www.twilio.com/console/voice/settings) to enable PCI Mode
- Navigate to <Pay> Connectors (https://www.twilio.com/console/voice/pay-connectors) under Programmable Voice and click on the Stripe tile. Click on Install
- Now set the Stripe Connector unique name to Default
- Click on "Connect with Stripe"

## Step 3: Set up Twilio <Pay> to capture payment details
- Navigate to TwiML Bins: https://www.twilio.com/console/runtime/twiml-bins
- Friendly Name: <Pay> with Stripe

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Say>Calling Twilio Pay</Say>   
  <Pay chargeAmount="20.45"/>
</Response>
```

## Step 4: Create a Twilio Function to handle payment (app.js)
- Navigate to Twilio Functions: https://www.twilio.com/console/functions/manage
- Click on Create Function > Blank Template > Create
- Rename function to "pay"
- Rename your function to "pay" and update the path to /pay. This will be the URL that hosts this code for you.
- Select 'Incoming Voice Calls" as your event
- Now you can remove the boilerplate code and paste the following code into your Function:

```
exports.handler = function(context, event, callback) {
    console.log("in Pay");
    console.log(event);
    console.log(event.Result);
    
	let twiml = new Twilio.twiml.VoiceResponse();
	
	switch (event.Result) {
    case "success":
        text = "Thank you for your payment";
        break;
    case "payment-connector-error":
        text = "The Payment Gateway is reporting an error";
        console.log(decodeURIComponent(event.PaymentError));
        break;
    
    default: 
        text = "The payment was not completed successfully";
}
	twiml.say(text);
	callback(null, twiml);
};
```
- Save your Function. This action fully deploys your Function.

## Step 5: Update TwiML with Function URL in the action parameter
- Copy the URL for your new Function, then head back to the TwiML bin you created in the last step.
- Go to https://www.twilio.com/console/twiml-bins and update your code to include your Function URL in the action parameter. 

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Say>Calling Twilio Pay</Say>   
  <Pay chargeAmount="20.45"
    action="https://urobilin-wolf-1967.twil.io/pay"/>
</Response>
```

## Step 6: Purchase a voice-enabled Twilio phone number
- Navigate to https://www.twilio.com/console/phone-numbers/incoming
- Scroll to the "Voice & Fax" section. Double-check that "Accept Incoming" is set to Voice Calls, and Configure With is set to "Webhooks, TwiML Bins...".
- "A call comes in", select "TwiML" from the drop-down. Select "<Pay> with Stripe"
- Save

## Step 7: Test capturing credit card details and see the resulting charge on your Stripe account
- Dial Twilio #: +65 3138 5510
- 4242 4242 4242 4242
- 12/20
- 94105
- 333

## Step 8: Verify w/ call Logs
- https://www.twilio.com/console/voice/calls/logs

## Step 9: Confirm Payment in Stripe
- https://dashboard.stripe.com/test/payments?status%5B%5D=successful

## References
- https://www.twilio.com/pay
- https://www.twilio.com/blog/pay-generally-available
- https://www.twilio.com/blog/create-voice-based-reservation-system-laravel-php-twilio-pay
- https://www.youtube.com/watch?v=MNh4BesTltM
- https://www.twilio.com/docs/voice/tutorials/how-capture-your-first-payment-using-pay
