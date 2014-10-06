## MongoDB Tutorial

In order to get used to mongoDB and it's quirks, we will play around with the mongo [shell](http://docs.mongodb.org/v2.2/mongo/).  The mongo shell is somewhat similar to the Postgres shell, albeit has a very (very) different syntax.  Part of this is due to the differences between a document database and a relational database.

### References

Throughout this tutorial use these references/cheatsheets to speed up the transition from SQL:
* [SQL -> MongoDB mapping][1]
* [MongoDB Cheatsheet][2]
* [MongoDB Reference Cards][22]

### Exercise

For this tutorial we will be using a raw feed of government website click-stream [data][11] from bitly.  We will get experience with MongoDB through it's shell and learn many of the basic operations.

__Save your commands in a text file, which you will submit__

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

#### Part 1

1. What database are you currently connected to?
2. What other databases are there?
3. Create a new database called `clicks` for our click-stream data
4. Double check that the database was created correctly and that you are currently 'using' it.

Time for some fun... we will now begin to insert data into our database.  MongoDB uses an extension to JSON called [BSON](http://www.mongodb.com/json-and-bson) which adds support for more complex data types (dates, binary objects, ids, etc.).  Usually you need not concern yourself with the specific difference.

1. Quit out of the mongo shell: `quit`
2. Just kidding (this is Javascript remember?): `quit()`

In order to get data into our database we will exploit one of the niceties of its flexibility and one of the __few__ reasons to use it over SQL.  MongoDB (being the schemaless database it is) can basically accept any data on the fly (as long as it is properly formed JSON, CSV, or TSV) and create a schema for it.

#### Part 2
2. Using [curl][7] on the commandline, connect to the government data feed: http://developer.usa.gov/1usagov 
3. print it to STDOUT (the terminal). Use the `-s` flag to silence the status bar. 
    * Since it is a stream of data curl will keep the connection open.  Watch the data stream to your terminal STDOUT.
4. Once you have it printing to the terminal, using UNIX [pipes][10] we can pipe them directly into a mongodb using with  [`mongoimport`][9] command.  *FYI:   using UNIX `>` command you can pass it into a text file.*
5. If you are comfortable using bash and shell you can parse your data with those now, like, even before you pass it into the monogdb, or you can just clean it directly in mongodb. 
6. For starters, pipe the result of the curl command to [`grep`][12] to filter the stream for clicks from NASA's site.  You need to use the `--no-buffer` flag for the command to output intermediate results.
7. Now that we can get the JSON and have a handle on pipes, we are ready to put all the pieces together with `mongoimport`.  Things are about to get crazy, hold on to your seats...  Remember how we created a `clicks` database?  Well forget we ever did.
8. on the commandline, this is how the you should set up your monogimport command.  Put the name of your database after the `--db` part , and the name of the collection after the `--collection` part. <br> `$ curl stuff goes here |  mongoimport --db <database name> --collection <collection name>`

#### Part 3

1. Curl the [government stream](http://dev.bitly.com/public_data.html) (1.USA.gov) #nofilter (i.e. the entire stream), pipe it to `mongoimport`, and import it into a database called `prism` in a collection called `bitly_clicks` (remember... no schemas, no masters). Collect data for 5 minutes or so. Remember at what time you began collecting data.
2. Use the mongo shell (`mongo`) to connect to the database.
3. Inspect your databases.  How many are there and what are their sizes?
4. Inspect the collections, how much data did you collect in your `bitly_clicks` collection? Remember to use your help: 
    * `help`
    * `db.help()`
    * and... `db.<collection_name>.help()`

![mind_blown](http://i.imgur.com/j74SykU.gif)

So now that we have data, we can begin querying! Oh, and mongo's shell has autocomplete... so `<TAB>` and `<TAB> <TAB>` your way to mastery.

__If you get stuck with reading from the stream, feel free to import one of the static [archive files][23]__

#### Part 4

1. First, print out all of the clicks you have stored using `find()`
1. Count how many documents are in your collection?
2. There may be a lot, limit how many are returned with `limit()`. Return only 25.
3. Using `find()`, get a bit more specific in your query.  Find all clicks from San Francisco.  How many are there? (if you did not get any from SF, count the clicks from California)

#### Part 5

4. We have a timestamp field, but right now it is just a string.  Using an [`update()`][14] command and a [`forEach()`][16] loop, convert all of the timestamps into [BSON date][15] objects.
5. Now that we have true date objects, we can [query][17] our clicks by when they were clicked.  Since we have collected data in such a small time window this will be slightly artificial, but count how many clicks happened in the first minute of your data collection.
    * You may need to find the earliest click to know when you started collecting data.

#### Part 6

2. Using the mongoDB [aggregation][13] functionality, find what the most popular link clicked is? You will need to use `$group`, `$sum`, and `$sort`.

## Extra Credit

MongoDB actually has some [geospatial][19] facilities (don't worry, Postgres has [even better][18] ones as well).  Using the geoindices and querying, answer the following:

1. All clicks within 50 miles of San Francisco
2. All clicks that came from [New England](http://en.wikipedia.org/wiki/New_England)

#### CartoDB

[CartoDB][20] happens to be one of my favorite tools for geospatial analysis (with built in PostGIS querying).  Map the clicks across the globe.  Visualize clicks over time with a [torque map][21].

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
[11]: http://www.usa.gov/About/developer-resources/1usagov.shtml
[12]: http://www.tutorialspoint.com/unix/unix-pipes-filters.htm
[13]: http://docs.mongodb.org/manual/reference/sql-aggregation-comparison/
[14]: http://stackoverflow.com/a/2900761
[15]: http://armyofrobots.tumblr.com/post/12645585096/query-mongodb-using-timestamp
[16]: http://stackoverflow.com/a/16918970
[17]: http://cookbook.mongodb.org/patterns/date_range/
[18]: http://postgis.net/
[19]: http://docs.mongodb.org/manual/administration/indexes-geo/
[20]: http://cartodb.com/
[21]: http://blog.cartodb.com/post/66687861735/torque-is-live-try-it-on-your-cartodb-maps-today
[22]: http://info.mongodb.com/rs/mongodb/images/mongodb_qrc_booklet.pdf
[23]: http://1usagov.measuredvoice.com/
