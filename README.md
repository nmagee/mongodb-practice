# Lab: Practice with MongoDB

Learn MongoDB with hands-on practice.

- [Lab: Practice with MongoDB](#lab-9-practice-with-mongodb)
  - [Run MongoDB \& Connect](#run-mongodb--connect)
  - [Exercise 1](#exercise-1)
  - [Exercise 2](#exercise-2)
  - [Reference: CRUD Operations](#reference-crud-operations)
  - [Integrating with `python3`](#integrating-with-python3)
  - [Learn More](#learn-more)
  - [Submit](#submit)

## Fork This Repository

To make and save changes to this project, [create a fork of this repository](https://github.com/nmagee/mongodb-practice/fork) and open it within a Codespace.

## Install, Set Up MongoDB & Connect

**Setup**

This course will be using MongoDB Atlas, a cloud-based Mongo service, for hands-on exercises. If you would like your own Atlas cluster, follow the instructions in the [**How To video**](https://www.youtube.com/watch?v=9DbZ2ii01ew) for setup. I recommend using a Google account when creating your [**Atlas MongoDB Cluster**](https://www.mongodb.com/cloud/atlas/register).

For this lab use the MongoDB Atlas cluster that you created:

1. Open this repository in GitHub Codespaces

2. Run the setup script (if necessary)

    ```
    wget -qO- https://gist.githubusercontent.com/nmagee/9e868ac38f13e559e073a1b8e7fb4f70/raw/c4c370dac784878da6198237c4b45290e684dde8/install.sh | bash
    ```

3. You will now have a CLI tool `mongosh` that can be used to connect to your MongoDB Atlas cluster.

4. Provide credentials by setting three environment variables in your `~/.bashrc` file:

    ```
    export MONGOHOST=""   # should look something like `mongodb+srv://cluster0.XXXX.mongodb.net` - remove username/password if present.
    export MONGOPASS=""   # the db username you created with your Atlas cluster.
    export MONGOUSER=""   # the db password you set when you created your Atlas cluster.
    ```

5. Refresh `bash` by sourcing your `~/.bashrc` file:

    ```
    source ~/.bashrc
    ```

**Connect using the CLI**

You can now connect to your DB with the `mongosh` command using your `env` parameters like this:
```
mongosh $MONGOHOST --apiVersion 1 --username $MONGOUSER --password $MONGOPASS
```

Run that command and you should get a prompt like this:
```
Current Mongosh Log ID:	66158541454b7620e4a690a6
Connecting to:		mongodb+srv://<credentials>@cluster0.pnxzwgz.mongodb.net/?appName=mongosh+2.2.3
Using MongoDB:		7.0.8 (API Version 1)
Using Mongosh:		2.2.3

For mongosh info see: https://docs.mongodb.com/mongodb-shell/

Atlas atlas-2o6kes-shard-0 [primary] test>
```

> Note that you could copy this connection command and write it as an alias in your `~/.bashrc` file as an additional shortcut.

## Watch

After watching the [**Mongo in 30 minutes**](https://www.youtube.com/watch?v=pWbMrx5rVBE) video, try completing both of 
these exercises.

## CLI Exercise 1

This task uses sample data available in the [**Atlas Cloud console**](https://cloud.mongodb.com/v2).

1. From within the Atlas Cloud console, load sample data using the `...` button from within your cluster settings. Look for the "Load Sample Dataset" option in the drop-down list.
   
2. Using your `mongosh` shell, list your databases, select the `sample_mflix` set, then show collections within that:
```
show dbs;
use sample_mflix;
show collections;
```

3. This should show you there is a `movies` collection within that database. Find all documents in the collection, then count them:
```
db.movies.find();
db.movies.countDocuments();
```

4. Search for all documents containing a `year` of `1921` and count the results. Then display the results:
```
db.movies.find({"year":1921})
db.movies.countDocuments({"year":1921})
```

5. Retrieve a single document based on `ObjectId`:
```
db.movies.find(ObjectId('573a1391f29313caabcd72f0'));
```

6. Finally, using the code below insert a new document. After insertion, can you retrieve this document?
```
db.movies.insertOne({
    genres: [ 'Drama' ],
    runtime: 14,
    cast: [
        'Acty Actor',
    ],
    title: "The Big Bad Movie",
    countries: [ 'USA' ],
    released: ISODate('1930-01-01T00:00:00.000Z'),
    directors: [ 'Neal Magee' ],
    lastupdated: '2024-04-09 01:12:08.943000000',
    year: 2025,
    imdb: { rating: 8.1, votes: 1455, id: 12999 },
    type: 'movie',
    num_mflix_comments: 0
});
```

## CLI Exercise 2

This exercise walks you through creating your own database and populating a collection with your own data.

1. List DBs
```
show dbs
```
2. Show the DB you are currently in
```
db
```
3. Use a specific database based on your UVA computing ID. If it does not exist, it will be created.
```
use mst3k
show dbs
```
Notice that your new database does not yet show up. This is because it needs to contain some documents first.

4. Insert a simple document. You will specify a collection within the `things` DB, and if it does not yet exist it will be created.
```
db.hobbies.insertOne({name:"horseback riding"})
```
5. List all documents in this collection.
```
db.hobbies.find()
```
You should get back a simple document:
```
{
    "_id" : ObjectId("606b5e9d37c1606354c39e3d"),
    "name" : "horseback riding",
}
```

6. Insert several more documents, varied in their data complexity.
```
db.hobbies.insertOne({"name":"cycling","equipment":["bicycle","helmet","air pump"]})
db.hobbies.insertOne({"name":"basketball","equipment":["ball","shoes","court","rim","game"]})
db.hobbies.insertOne({"name":"archery","equipment":["bow","arrows"]})
```
7. View all documents again:
```
db.hobbies.find()
```
8. Notice your first document lacks any equipment values. To update it
```
db.hobbies.updateOne({name:"horseback riding"},{$set : {equipment:["horse","saddle","helmet"]}})
```

9. Search for that record to see if it has been updated properly:

```
db.hobbies.find({name:"horseback riding"})
```
```
{
    "_id" : ObjectId("606b5e9d37c1606354c39e3d"),
    "name" : "horseback riding",
    "equipment" : [ "horse", "saddle", "helmet" ]
}
```

10. Search for all hobbies that require a helmet:
```
db.hobbies.find({equipment:"helmet"})
```
1.  Upsert - adds a document when it does not exist from an `UPDATE` command
```
db.hobbies.update({name:"ultimate frisbee"},{name:"ultimate frisbee",equipment:["friends","frisbee"]},{upsert: true})
```
1.  Remove a document
You can remove document based on any `find` parameters, such as a particular value. However, the most unique key for
single-row deletions is the `_id` of a particular row. Try deleting some documents (replace the `ObjectId` value with one
from your collection:

```
# Remove by the _id:ObjectId
db.hobbies.remove({_id:ObjectId('606b5e9d37c1606354c39e3d')})

# Or use deleteOne if you know another unique value
db.hobbies.deleteOne({name:"archery"})

# Or deleteMany based on a common attribute
db.hobbies.deleteMany({equipment:"helmet"})
```

## Reference: CRUD Operations

Let's create another collection to work with called `inventory`:
```
db.inventory.insertMany( [
   { item: "canvas", qty: 100, size: { h: 28, w: 35.5, uom: "cm" }, status: "A" },
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "mat", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
   { item: "mousepad", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "P" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
   { item: "sketchbook", qty: 80, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "sketch pad", qty: 95, size: { h: 22.85, w: 30.5, uom: "cm" }, status: "A" }
] );
```

**Create**
```
db.<db-name>.insertOne({...})
db.inventory.insertOne({ item: "calendar", qty: 80, size: { h: 20, w: 40, uom: "cm" }, status: "D" })

db.<db-name>.insertMany({...})   # See above
```

**Read**
```
db.<db-name>.find({...})
db.inventory.find({"status": "D"})

db.<db-name>.findOne({...})
db.inventory.findOne(ObjectId('66157d66f27a5c78964fdd80'))
```

**Update / Upsert**

An `upsert` is applied to all documents matching the criteria, or inserts a new document if there are no matches.

```
db.<db-name>.update({{"<search-key>" : "<search-value>"},{$set : {"<key>": "<updated-value>"}}})
db.<db-name>.updateOne({SingleKeyToUpdate},{Set Command})

db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```

**Delete**
```
db.<db-name>.deleteOne(<search-condition>)
db.<db-name>.delete(<search-condition>)

db.inventory.deleteMany({ status : "A" })
```

## Integrating with `python3`

Try installing the `pymongo` library! The same operations will work in Python.

Change into the [`python/`](python/) directory to explore simple CRUD operations in MongoDB. 

## Learn More

- [MongoDB Tutorials](https://www.mongodb.com/docs/manual/tutorial/)

## Submit

Using the MongoDB Atlas connection we worked with in class, switch to the database you created under your UVA computing ID.

Write a single Python script that

1. Creates a new collection,
2. inserts five (5) documents, and then
3. write a query that displays exactly three (3) of those documents. 

Be creative with data values or other attributes to make your `find` or `find_many` statements easy.

Save this file as a new GitHub gist here: https://gist.github.com/ and submit the URL for grading.
