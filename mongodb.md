## MongoDB Tutorial

In order to get used to mongoDB and it's quirks, we will play around with the mongo [shell](http://docs.mongodb.org/v2.2/mongo/).  The mongo shell is somewhat similar to the Postgres shell, albeit has a very (very) different syntax.  Part of this is due to the differences between a document database and a relational database.

### References

Throughout this tutorial use these references/cheatsheets to speed up the transition from SQL:
* [SQL -> MongoDB mapping][1]
* [MongoDB Cheatsheet][2]

### Exercise

For this tutorial we will be using the raw feed of the Bay Area Bikeshare.  We will get experience with MongoDB through it's shell and learn many of the basic operations.

First things first, let us setup our database.  Just like Postgres, mongoDB has a server process which you connect to with a client:

![mongo arch][3]

In this exercise our client will be the mongo shell.  This happens to be a Javascript shell (and can execute any arbitrary Javascript code -- like creating functions). There are also many other [client libraries][4] for most any programming language out there.

#### Getting Started

1. Make sure the mongoDB server process is running by trying to connect to it with the shell client: `mongo`
    * If this works it is running on `localhost:27017`
    * If it complains you need to start the process: `mongod`
2. Connect to the server daemon (background) process (if you haven't already) with the mongo shell client: `mongo`
3. Display both mongo help and the database help: `help` and `db.help()` respectively

#### Data Model

MongoDB has many similar concepts to Postgres, you can use the official [docs][1] as a reference to map them.  There are databases with collections each of which have documents containing multiple fields.

![mongo diag][5]

The biggest glaring difference between mongodb and SQL...

> MongoDB is a document-oriented DBMS. Think of MySQL but with JSON-like objects comprising the data model, rather than RDBMS tables. Significantly, MongoDB supports neither joins nor transactions. However, it features secondary indexes, an expressive query language, atomic writes on a per-document level, and fully-consistent reads.

* No schema
* No joins
* No transactions

<img src="http://www.thevisualist.org/wp-content/uploads/2013/05/Butcher_GodsMasters_HighRes.jpg" height=500>

_Unless you shard..._

Using the following [guide][6] as a reference, begin exploring the database. Mongo (being the very flexible database it is) can create databases, collections, documents, etc. on the fly.  For example, to create a new database simply try to use the database you haven't created: `use my_new_database`

And __POOF__ it comes into existence (this also happens with collections)!

1. What database are you currently connected to?
2. What other databases are there?
3. Create a new database called `bikeshare` for our Bikeshare data
4. Double check that the database was created correctly and that you are currently 'using' it.

Time for some fun... we will now begin to insert data into our database.  MongoDB uses an extension to JSON called [BSON](http://www.mongodb.com/json-and-bson) which adds support for more complex data types (dates, binary objects, ids, etc.).  Usually you need not concern yourself with the specific difference.

1. Quit out of the mongo shell: `quit`
2. Just kidding (this is Javascript remember?): `quit()`

In order to get data into our database we will exploit one of the niceties of its flexibility and one of the __few__ reasons to use it over SQL.  MongoDB (being the schemaless database it is) can basically accept any data on the fly (as long as it is properly formed JSON, CSV, or TSV) and create a schema for it.

1. Using [curl][7] on the commandline, download the bikeshare [data][8] and print it to STDOUT (the terminal).
2. Once you have it printing to the terminal, we can use UNIX [pipes][10] to pass the data to the [`mongoimport`][9] command.  For starters, pipe the result of the curl command to `less`.

Now that we have the JSON and have a handle on pipes, we are ready to put all the pieces together with mongoimport.  Things are about to get crazy, hold on to your seats...  Remember how we created a `bikeshare` database?  Well forget we ever did.

1. Curl the bikeshare data, pipe it to `mongoimport`, and import it into a database called `flywheel` in a collection called `bike_stations` (remember... no schemas, no masters).
2. Use the mongo shell (`mongo`) to connect to the database.
3. Inspect your databases.  How many are there and what are their sizes?
4. Inspect the collections, how much data is the bikeshare data? Remember to use your help: `help` and `db.help()` and... `db.<collection_name>.help()`

![mind_blown](http://i.imgur.com/j74SykU.gif)

### Additional GUI clients

Here are some additional GUI clients if you so want to try (my favorite is RoboMongo):
* [Robomongo (Multiplatform)](http://robomongo.org/)
* [MongoHub (Mac OSX)](https://github.com/fotonauts/MongoHub-Mac) with down-loadable [binary](https://mongohub.s3.amazonaws.com/MongoHub.zip)
* [Humongous (web based)](https://github.com/bagwanpankaj/humongous)

[1]: http://docs.mongodb.org/manual/reference/sql-comparison/
[2]: https://blog.codecentric.de/files/2012/12/MongoDB-CheatSheet-v1_0.pdf
[3]: http://www.infoq.com/resource/articles/mongodb-java-php-python/en/resources/non-sharded-client-connection.png
[4]: http://docs.mongodb.org/ecosystem/drivers/
[5]: http://assets.zipfianacademy.com/data/images/mongo_diagram.png
[6]: http://docs.mongodb.org/manual/tutorial/getting-started/
[7]: http://httpkit.com/resources/HTTP-from-the-Command-Line/
[8]: http://www.bayareabikeshare.com/stations/json
[9]: http://docs.mongodb.org/manual/reference/program/mongoimport/#use
[10]: http://www.ee.surrey.ac.uk/Teaching/Unix/unix3.html