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

MongoDB just like Postgres (or any database really) uses a binary format to store the data contained within it.  There is a specified database file (defaults to `/data/db) which you can [set][3.1] in its configuration.  But you should not think of it in this way, the client-server abstraction is quite powerful and anytime you need to put data in MongoDB or take data out you must go through the gatekeeper (the client ex: `mongo`).

#### SQL vs. NoSQL

Contrary to what some folks may want, NoSQL does not stand for

> NO SQL!!!

but rather, not only SQL (a much more accepting acronym).  In this spirit, it emerged out of the need for something more (or rather less) than SQL.  Something more flexibly for the new world of messy data.

#### Data Model

MongoDB has many similar concepts to Postgres, you can use the official [docs][1] as a reference to map them.  There are databases with collections each of which have documents containing multiple fields.

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

#### Live code



* Database
* collections
* docuements
* fields
* what is a cursor

__Fields are specified at document (not collection) level__

Mongo vs SQL

* No schema
* No joins
* No transactions

creates collections and database on fly

help
and db.help()
<tab> <tab>

UNIX interlude: curl and pipes: `ls | grep`

mongoimport

find() semantics and matching

Mongo
* client server
* cursors
* denormalization
* associating records: foreign keys

** Example code stubs for operations **

SQL
* create table schema
* insert rows
* connectors



--


## The Web

### Client/Server Model
### HTTP Request Cycle

### HTML

### CSS

## APIs

### ReST

### HATEOS



[3]: http://www.infoq.com/resource/articles/mongodb-java-php-python/en/resources/non-sharded-client-connection.png
[3.1]: http://stackoverflow.com/a/7287459
[4]: http://docs.mongodb.org/ecosystem/drivers/
[5]: http://assets.zipfianacademy.com/data/images/mongo_diagram.png
[6]: http://docs.mongodb.org/manual/tutorial/getting-started/
