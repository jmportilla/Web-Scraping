# Your Guide to Web scraping

## MongoDB (and NoSQL)

First things first, let us setup our database.  Just like Postgres, mongoDB has a server process which you connect to with a client:

![mongo arch][3]

In this exercise our client will be the mongo shell.  This happens to be a Javascript shell (and can execute any arbitrary Javascript code -- like creating functions). There are also many other [client libraries][4] for most any programming language out there.

#### Getting Started

1. Make sure the mongoDB server process is running by trying to connect to it with the shell client: `mongo`
    * If this works it is running on `localhost:27017`
    * If it complains you need to start the process: `mongod`
2. Connect to the server daemon (background) process (if you haven't already) with the mongo shell client: `mongo`
3. Display both mongo help and the database help: `help` and `db.help()` respectively

#### Where is the Data?

MongoDB just like Postgres (or any database really) uses a binary format to store the data contained within it.  There is a specified database file (defaults to `/data/db`) which you can [set][3.1] in its configuration.  But you should not think of it in this way, the client-server abstraction is quite powerful and anytime you need to put data in MongoDB or take data out you must go through the gatekeeper (the client ex: `mongo`).

#### SQL vs. NoSQL

Contrary to what some folks may want, NoSQL does not stand for

> NO SQL!!!

but rather, not only SQL (a much more accepting acronym).  In this spirit, it emerged out of the need for something more (or rather less) than SQL.  Something more flexibly for the new world of messy data.

#### Data Model

MongoDB has many similar concepts to Postgres, you can use the official [docs][1] as a reference to map them.  There are databases with collections (tables) each of which have documents (rows) containing multiple fields (columns).

![mongo diag][5]

The biggest glaring difference between mongodb and SQL...

> MongoDB is a document-oriented DBMS. Think of MySQL but with JSON-like objects comprising the data model, rather than RDBMS tables. Significantly, MongoDB supports neither joins nor transactions. However, it features secondary indexes, an expressive query language, atomic writes on a per-document level, and fully-consistent reads.

* No schema
* No joins
* No transactions

<img src="http://www.thevisualist.org/wp-content/uploads/2013/05/Butcher_GodsMasters_HighRes.jpg" height=500>

_Unless you shard...but that's another story_

Using the following [guide][6] as a reference, begin exploring the database. Mongo (being the very flexible database it is) can create databases, collections, documents, etc. on the fly.  For example, to create a new database simply try to use the database you haven't created: `use my_new_database`

And __POOF__ it comes into existence (this also happens with collections)!

#### Quickstart

First let us explore our MongoDB instance. The best way to learn interactive is through the built-in help.  Typing `help` gives you the methods available globally in the shell.  `db.help()` shows you what functions are defined at the database level.  To see what databases you have: `show dbs`.  In my case it looks like I already have a few.  But we can create a new one by assuming it already exists and connecting to it: `use my_new_db`.

But notice it has not been created yet.  DBs (and collections) only get created when you insert data into them.  Strangely we can still inspect it though.  You can look at the collections you have contained in your database with `db.getCollectionNames()`.  This is as good a moment as any to mention that the `mongo` shell tab autocomplete.  Well it looks like the new database we just created has no collections... which is comforting.

Let us add data!  While you can add data through a driver client (such as Python), for now we will be hand coding our own documents to insert.

Create a new collection named `users` and start inserting some data:

```javascript
db.users.insert({ name: 'Jon', age: '45', friends: [ 'Henry', 'Ashley']})

show dbs
db.getCollectionNames()
```
We can see now that we have a `my_new_db` database, a users collection, and an index collection (for our entire database).  Let us add two more users:

```javascript
db.users.insert({ name: 'Ashley', age: '37', friends: [ 'Jon', 'Henry']})
db.users.insert({ name: 'Frank', age: '17', friends: [ 'Billy'], car : 'Civic'})

db.users.find()
```

Two things of note here, the first is that we can specify different fields on a per document basis.  Frank has a `car` field while none of the other users do.  This is what we mean when we say MongoDB is schema-less.  In SQL, you would have to add a new column to your table and for every row that was already i nthe table insert some value (or NULL) for this new column.  The second thing is simply how to do a `SELECT * FROM ...` in MongoDB, a call to `find()`. You should also notice the _id field, MongoDB automatically creates this unique ID for us and indexes the collection on it.

#### Querying

MongoDB has a somewhat unique manner for querying and selecting our data.  The selector is a JSON object that MongoDB uses to pattern match the appropriate documents:

```javascript
// find by single field
db.users.find({ name: 'Jon'})

// find by presence of field
db.users.find({ car: { $exists : true } })

// find by value in array
db.users.find({ friends: 'Henry' })

// field selection (only return name)
db.users.find({}, { name: true })
```

In the first case we are simply searching for all users where the `name` field is equal to `Jon`.  Nothing too special here.  In the next case we retrieve all documents that have the `car` field present.  The third line does something quite convenient, arrays are actually first class citizens in MongoDB and this line finds any document that has a `friends` field with contains 'Henry' (since it is an array).  And the last line is how we only return certain fields of a document, the first argument to `find()` being the query and the second argument is the fields to return.  This line gives us the names of every user.

#### CRUD

Often you may here about __C__reate, __R__ead, __U__pdate, __D__elete with regards to web applications and databases.  We will see how this relates to HTTP and APIs later but for now we map these concepts to database operations.

| Operation | Database |
| :--: | :--: |
| Create | insert() |
| Read | find() |
| Update | update() |
| Delete | remove() |

We have touched on two of these.  Lets explore the other two.  Update() is quite similar to insert(), the only difference being that update() mutates an existing document.  Think of it as a find() and insert() in one.

Lets change Jon's friends: `db.users.update({name: "Jon"}, {friends: ["Phil"]})`.  If we do a find we can see that we did not update Jon but actually __replaced__ Jon's document (with one that contains a single field, `friends` that has a single value `["Phil"]`).  Sometimes this is very useful behavior, but most likely in this case this is not what we want.  Take 2!

```javascript
// replaces friends array
db.users.update({name: "Jon"}, { $set: {friends: ["Phil"]}})

// adds to friends array
db.users.update({name: "Jon"}, { $push: {friends: "Susie"}})

// upsert
db.users.update({name: "Stevie"}, { $push: {friends: "Nicks"}}, true)

// multiple updates
db.users.update({}, { $set: { activated : false } }, false, true)
```

In the third line we are first creating a new user "Stevie" if one does not exist and add "Nicks" to the friends array.  Notice that since we are using the upsert functionality of update we want to __$push__ to the friends array in case a user "Stevie" already exists (so we don't overwrite the whole friends array)

In the line we are simply telling MongoDB to update every document that matches the query.  Now that we have seen most of the basic functionality of MongoDB that we will need, lets touch on the last CRUD operation and get things back to how they were originally.

And to remove documents we use a similar syntax as `find()`.  Let us remove all of our users: `db.users.remove()`

#### Imports and Cursors

To have more interesting data to work with, let us import a data file to our database.  To do so we can use the commandline `mongoimport` command.  Exit out of your shell (if your are in one).  The `mongoimport` command can take a varity of inputs, but the most natural is JSON, all we need to do is simply specify a database, collections, and JSON file to import.

```bash
mongoimport --db tweets --collection coffee --file coffee-tweets.json
```

And to look at what we have imported: 

```javascript
use tweets
db.coffee.find()
```

Now that we have a lot of data something interesting has happened, mongoDB didn't return all of the records.  How many did it return?  Well it returned a cursor (think of a generator in Python) which represents our query and it provides a method to get more: `it`. 

Cursors actually have methods defined on them, here we count `db.coffee.find().count()` to see 122 documents are in our collection.  We can also `db.coffee.find().limit(2)` or `db.coffee.find({}).sort({ 'user.followers_count' :-1}).limit(3)` to get the most popular users.

But this is actually quite hard to explore as it is using the mongo shell.  Let us get back out and use another tool to explore our data.

#### Variety.js

[Variety.js][7] is a data exploration library or as it describes itself:

> This lightweight tool helps you get a sense of your application's schema, as well as any outliers to that schema. Particularly useful when you inherit a codebase with data dump and want to quickly learn how the data's structured. Also useful for finding rare keys.

It is very useful if you inherit a data dump or a mess of JSON which you want to better understand.  Even if you have collected your own data, it is very useful to get a sense of the distributions of keys for something like data from the Twitter stream (that can have varying structures).

It is simple and easy to use, just specify the database and collection and run the following in your terminal (__not__ the `mongo` shell):

```
mongo tweets --eval "var collection = 'coffee'" variety.js
```

And after we run it we see a stream of information. It outputs the number of documents in a collection with the given fields.  Usually you would hope every document has every field (at least if you are coming from the relational world), but here you can see that some of the more nuanced fields of our Twitter data (that are often nested objects) such as `place.name` and `geo.coordinates` are quite rare!

#### Iteration

MongoDB having a flexible data model also has a flexible shell/driver.  If you need to take some action based on a query or update documents you can use an iterator on the cursor to go document by document.  In the Javascript shell we can do this with Javascript's `forEach`.  Like Python's `for ... in`, Javascript actually has a more functional approach to this type of iteration and requires that you pass a callback (similar to map() and reduce()).

```javascript
db.tweets.coffee.find().forEach(function(doc){
    doc.entities.urls.forEach(function(url) {
        db.urls.insert({ 'url':url, 'user': doc.user });
    });
});
```

Here we are finding all the urls contained in our tweets and adding them to a new collections.  You most likely would do something more interesting like actually download those urls, but for simplicity sake we just add them to another collection.  With forEach the sky's really the limit!

#### Aggregation

In SQL, some of the most useful features of the language are the aggregates to compute useful statistics and metrics.  In MongoDB there are the same but in my opinion the querying/aggregation ability in MongoDB is not as elegant as SQL.

Let us say we want to find out how many total retweets we had for our tweets about coffee by each country.

```javascript
db.coffee.aggregate( [ { $group :
    {
        _id: "$place.country_code",
        total: { $sum: "$retweet_count" }
    }
}])

db.coffee.find({ retweet_count : { $gt : 0 }  })
```

First we group by `place.country_code`, then we sum the `retweet_count`.  And if we do a more specific find, we see that there actually we not any retweets!


[3]: http://www.infoq.com/resource/articles/mongodb-java-php-python/en/resources/non-sharded-client-connection.png
[3.1]: http://stackoverflow.com/a/7287459
[4]: http://docs.mongodb.org/ecosystem/drivers/
[5]: http://assets.zipfianacademy.com/data/images/mongo_diagram.png
[6]: http://docs.mongodb.org/manual/tutorial/getting-started/
[7]: https://github.com/variety/variety
