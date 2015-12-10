---
layout: docs
title: "Mongo - Developer Docs"
subheader: Use Mongo
subtitle:  Mongodb
subtitle2: Developer Docs
permalink: /docs/templates/mongo.html
---

This task is a good starter for connecting to MongoDB databases. Here at Joined Node, we recommend [MongoLab](http://mongolab.com)

First, we need to set up our variables, so add the following parameters to the [Encrypted Environment Variables](/docs/env/) section of your IDE:

1. `MONGO_URL` => `YOUR-MONGODB-URL`

These variables will be encrypted when you save, and only viewable either from your Task itself or when you edit it


Now, let's add our Node.js task code:

```javascript
var MongoClient = require('mongodb').MongoClient;
var waterfall   = require('async').waterfall;

module.exports = function(ctx, cb) {
	var MONGO_URL = ctx.env.MONGO_URL;
	if (!MONGO_URL) return cb(new Error('MONGO_URL secret is missing'))
	waterfall([
		function connect_to_db(done) {
			MongoClient.connect(MONGO_URL, function(err, db) {
				if(err) return done(err);
				done(null, db);
			});
		},
		function do_something(db, done) {
			db.collection('my-collection').insertOne({ msg: 'Hey Mongo!' }, function (err, result) {
				if(err) return done(err);
				done(null, result);
			});
		}
	], cb);
};
```

<br />

This code will use the `Environment Variables` we entered earlier as `context.env.{key}` variables to connect to your mongodb database, and then will insert a new single record to `my-collection` containing `{ msg: 'Hey Mongo!' }`.

This is for testing, but you can see how it works.