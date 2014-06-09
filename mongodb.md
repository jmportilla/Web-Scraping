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

In this exercise our client will be the mongo shell.  There are also many other [client libraries][4] for most any programming language out there.

#### Getting Started

1. Make sure the mongoDB server process is running by trying to connect to it with the shell client: `mongo`
    * If this works it is running on `localhost:27017`
    * If it complains you need to start the process: `mongod`
2. Connect to the server daemon (background) process (if you haven't already) with the mongo shell client: `mongo`

#### Data Model

MongoDB has many similar concepts to Postgres, you can use the official [docs][1] as a reference to map them.  There are databases with collections each of which have documents containing multiple fields.

![mongo diag][5]

The biggest glaring difference between mongodb and SQL...

> MongoDB is a document-oriented DBMS. Think of MySQL but with JSON-like objects comprising the data model, rather than RDBMS tables. Significantly, MongoDB supports neither joins nor transactions. However, it features secondary indexes, an expressive query language, atomic writes on a per-document level, and fully-consistent reads.

* No joins
* No transactions

<img src="http://www.thevisualist.org/wp-content/uploads/2013/05/Butcher_GodsMasters_HighRes.jpg" height=200>

_Unless you shard..._


### Additional GUI clients

Here are some additional GUI clients if you so want to try (my favorite is RoboMongo):
* [Robomongo (Multiplatform)](http://robomongo.org/)
* [MongoHub )(Mac OSX)](https://github.com/fotonauts/MongoHub-Mac) with down-loadable [binary](https://mongohub.s3.amazonaws.com/MongoHub.zip)
* [Humongous (web based)](https://github.com/bagwanpankaj/humongous)

[1]: http://docs.mongodb.org/manual/reference/sql-comparison/
[2]: https://blog.codecentric.de/files/2012/12/MongoDB-CheatSheet-v1_0.pdf
[3]: http://www.infoq.com/resource/articles/mongodb-java-php-python/en/resources/non-sharded-client-connection.png
[4]: http://docs.mongodb.org/ecosystem/drivers/
[5]: http://assets.zipfianacademy.com/data/images/mongo_diagram.png