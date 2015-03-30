##MongoDB Installation

1. To install MongoDB, download the zip file that matches your operating system from 
   [here](http://www.mongodb.org/downloads?_ga=1.2370361.886345798.1422741448)

2. Much like Postgres, you will need to launch the server before using Mongo. 

   - If the directory `/data/db` does not exist, create it by: `mkdir -p /data/db`
   - To start the Mongo server: `sudo mongod`
   - Open a new terminal and type `mongo` to open up a Mongo shell
   - Type `show dbs;` to show the databases you have
   - You can exit by `exit`

3. Resources and quick references to Mongo commands:

   - [MongoDB Cheatsheet](https://blog.codecentric.de/files/2012/12/MongoDB-CheatSheet-v1_0.pdf)
   - [Mongo Docs](http://docs.mongodb.org/v2.2/mongo/)
   - [MongoDB Reference Cards](http://info.mongodb.com/rs/mongodb/images/mongodb_qrc_booklet.pdf)


##Praticing Mongo Queries 

To get familiar with MongoDB, we are going to load in some click-log data from a government website and do
some basic queries on the data. Write your queries in a text file which you will include in your pull request.
Paste and run the queries in the Mongo shell.

1. Navigate to the `data` directory and load in the data by 
   `mongoimport --db clicks --collection log < click_log.json`

2. In the Mongo shell, run `show dbs;` to make sure the `click` database is created. 
   Run `use clicks;` to make `clicks` the default database. 

4. Inspect the collection `log` in your database. How many entries are in the `log` collection? 
   
   If you are not sure about what command to use, you can access the help section by:
    - `help`
    - `db.help()`
    - `db.<collection_name>.help()`

   Mongo also has tab complete, so you can tab complete some of your commands for conveinience.  

5. Print out all of the clicks you have stored using `find()`. 
   Now using `limit()`, return 10 entries.
   
6. Use `find()` to find all the clicks where `cy` (city) is `San Francisco`. How many are there? 

7. Find the type of the `t` (timestamp) field. You can access the type of a field in an entry by
   `typeof db.log.findOne({'t': {$exists: true}}).t`. The field should be a `number` now.
   
   Convert the field to the date type. You would need to multiply the number by 1000 and then make it a
   `Date` object. Loop over each record using `forEach()` and `update()` the record (using the `_id` field) with the
   converted object. Confirm the data type has been converted.
   
8. Sort the clicks by time and find when the first click occured. How many click occured in the first hour ?
   Assign the earliest time and time at the one-hour bound to separate variables before writing the query.

9. Using the Mongo's [aggregation](http://docs.mongodb.org/manual/reference/sql-aggregation-comparison/)
   functionality, find what the most popular link clicked is? You will need
   to use `$group`, `$sum`, and `$sort`.

## Extra Credit

MongoDB actually has some [geospatial](http://docs.mongodb.org/manual/administration/indexes-geo/) facilities 
(Don't worry, PostGres has [even better](http://postgis.net/) ones).
Using the geoindices and querying, answer the following:

1. All clicks within 50 miles of San Francisco
2. All clicks that came from [New England](http://en.wikipedia.org/wiki/New_England)

#### CartoDB

[CartoDB](http://cartodb.com/) happens to be one of my favorite tools for geospatial analysis (with built in PostGIS querying).
Map the clicks across the globe.  Visualize clicks over time with a 
[torque map](http://blog.cartodb.com/post/66687861735/torque-is-live-try-it-on-your-cartodb-maps-today).

### Additional GUI clients

Here are some additional GUI clients if you so want to try (my favorite is RoboMongo):
- [Robomongo (Multiplatform)](http://robomongo.org/)
- [MongoHub (Mac OSX)](https://github.com/fotonauts/MongoHub-Mac) 
   with down-loadable [binary](https://mongohub.s3.amazonaws.com/MongoHub.zip)
- [Humongous (web based)](https://github.com/bagwanpankaj/humongous)
