---
layout: docs
title: "Send a Message to Slack - Developer Docs"
subheader: Send a Message to Slack
subtitle:  Send a Message to Slack
subtitle2: Developer Docs
permalink: /docs/templates/slack_notify.html
---

Being able to quickly send a message to your Slack channels is a handy task.

First, we need to set up our variables, so add the following parameters to the `Encrypted Environment Variables` section of your IDE:

1. `MY_SLACK_WEBHOOK_URL` => `https://myaccountname.slack.com/services/hooks/incoming-webhook?token=myToken`
2. `icon_url` => `http://mysite.com/logo.png`

These variables will be encrypted when you save, and only viewable either from your Task itself or when you edit it

Now, let's add our Node.js task code:

```javascript
return function (context, callback) { 
	var required_env_params = ['MY_SLACK_WEBHOOK_URL','icon_url'];
	for (var p in required_env_params)
		if (!context.env[required_env_params[p]])
			return callback(new Error('The `' + required_env_params[p] + '` parameter must be provided in your env   settngs.'));

	var required_params = ['channel', 'text', 'username'];
	for (var p in required_params)
		if (!context.data[required_params[p]])
			return callback(new Error('The `' + required_params[p] + '` parameter must be provided.'));


	var slack = require('slack-notify')(context.env.MY_SLACK_WEBHOOK_URL);

	slack.send({
		channel: context.data.channel,
		icon_url: context.env.icon_url,
		text: context.data.text,
		unfurl_links: 1,
		username: context.data.username
	}, function (err) {
		if (err) {
			callback('API error:', err);
		} else {
			callback(null, {result:'Message received'});	
		}
	});
};
```
<br />
This code will use the `Environment Variables` we entered earlier as `context.env.{key}` variables to connect to Slack, and then will use the `context.data.channel`, `context.data.text` and `context.data.username` to send the message we've passed to it.

Once this has been setup, all you have to do is trigger a call to this task's unique URL with the variables of `channel`, `text` and `username` and it will trigger a Slack notification message being sent to the Slack channel specified in `channel`.