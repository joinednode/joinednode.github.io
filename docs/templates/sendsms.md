---
layout: docs
title: "Send SMS - Developer Docs"
subheader: Send An SMS with Twilio
subtitle:  Send SMS
subtitle2: Developer Docs
permalink: /docs/templates/send_sms
---

This task is a handy one that we use daily on various projects.

First, we need to set up our variables, so add the following parameters to the `Encrypted Environment Variables` section of your IDE:

1. `TWILIO_AUTH_TOKEN` => `YOUR-TWILIO-TOKEN`
2. `TWILIO_ACCOUNT_SID` => `YOUR-TWILIO-ACCOUNT-SID`
3. `TWILIO_NUMBER` => `A-NUMBER-FROM-YOUR-TWILIO-ACCOUNT`

These variables will be encrypted when you save, and only viewable either from your Task itself or when you edit it


Now, let's add our Node.js task code:

```javascript
var twilio = require('twilio');
module.exports = function (context, callback) {
	var required_env_params = ['TWILIO_AUTH_TOKEN', 'TWILIO_ACCOUNT_SID', 'TWILIO_NUMBER'];
	for (var p in required_env_params){
		if (!context.env[required_env_params[p]]){
			return callback(new Error('The `' + required_env_params[p] + '` parameter must be provided in your env   settngs.'));
		}
	}

	var required_params = ['to', 'message'];
	for (var p in required_params){
		if (!context.data[required_params[p]]){
			return callback(new Error('The `' + required_params[p] + '` parameter must be provided.'));
		}
	}
	
	var twilio_client = new twilio.RestClient(context.env.TWILIO_ACCOUNT_SID, context.env.TWILIO_AUTH_TOKEN);
	twilio_client.messages.create({
		to: context.data.to,
		from: context.env.TWILIO_NUMBER,
		body: context.data.message
	}, function(error, message) {
		if (error) {
			callback(error.message);
		} else {
			callback(null,message);
		}
	});
}
```
<br />
This code will use the `Environment Variables` we entered earlier as `context.env.{key}` variables to connect to Twilio, and then will use the `context.data.to` and `context.data.message` to send the message we've passed to it.