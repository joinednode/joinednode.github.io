---
layout: docs
title: "Insert or Update into MongoDB - Developer Docs"
subheader: Insert or Update into  MongoDB
subtitle:  Insert or Update into MongoDB
subtitle2: Developer Docs
permalink: /docs/templates/mongo_upsert.html
---

This task is a good starter for connecting to MongoDB databases. Here at Joined Node, we recommend [MongoLab](http://mongolab.com)

First, we need to set up our variables, so add the following parameters to the [Encrypted Environment Variables](/docs/env/) section of your IDE:

1. `MONGO_URL` => `YOUR-MONGODB-URL`

These variables will be encrypted when you save, and only viewable either from your Task itself or when you edit it


Now, let's add our Node.js task code:

```javascript
"use latest";

var MongoClient = require('mongodb').MongoClient;

module.exports = function mongoUpsert(context, callback) {
	var { MONGO_URL } = context.env;
	if (!MONGO_URL) return callback(new Error('MONGO_URL secret is missing'))
	
	var collection = context.data.collection;
	var query = context.data.query;
	var dataToUpsert = context.data.dataToUpsert;
	
	MongoClient.connect(MONGO_URL, function(err, db){
		if(err) return callback(err);
		db.collection(collection).update(query, { $set: dataToUpsert }, {upsert: true}, function(err, result){
			if(err) return callback(err);
			db.close();
			callback(null, result);
		});
	});
};
```

<br />

We will pass three fields into this task:

- **collection**: The name of the db collection where you want to upsert the data
- **query**: The query for the object you want to update. If this query throws no results, then the `dataToUpsert` will be inserted into the collection
- **dataToUpsert**: Arbirtrary mongodb valid data to upsert in the collection

This task will connect to the mongodb indicated by `context.env.MONGO_URL` and will then use the collection defined by `context.data.collection`.

This task will run the query defined by `context.env.query` and will either insert or update the record using the data passed inside the `context.data.dataToUpsert`.

We then close our database connection and return the result of the query via our callback function.