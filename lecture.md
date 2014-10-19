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


## Exercise: Wikipedia++


```python
# Import pylab to provide scientific Python libraries (NumPy, SciPy, Matplotlib)
import pylab

# import Python's standard library modules for regular expresions and json
import re
import json

# IF ON Python 3: from urllib.parse import urlparse
# This is useful to fix malformed urls 
import urlparse

# import the Image display module
from IPython.display import Image

# inline allows us to embed matplotlib figures directly into the IPython notebook
%pylab inline
```

    Populating the interactive namespace from numpy and matplotlib


    WARNING: pylab import has clobbered these variables: ['pylab']
    `%matplotlib` prevents importing * from pylab and numpy


### Step 1: Access the Wikipedia API

Lucky for us the Wikipedia [API](http://www.mediawiki.org/wiki/API) is well
[documented](http://www.mediawiki.org/wiki/API:FAQ).  And you do not need an API
key to access it (isn't that nice of them).  Go ahead, give it a spin!


```python
# import the Requests HTTP library
import requests

# A User agent header required for the Wikipedia API.
headers = {'user_agent': 'DataWrangling/1.1 (http://zipfianacademy.com; class@zipfianacademy.com)'}
```


```python
# Experiment with fetching one or two pages and examining the result (fill in URL and payload)
url = 'http://en.wikipedia.org/w/api.php'

# parameters for the API request
payload = { 'action' : 'parse' , 'format' : 'json','page' : "Zipf's law" }

# make the request
r = requests.post(url, data=payload, headers=headers)

# print out the result of the request as JSON
r.json();
```

__HINT: Check out the
[parse](http://www.mediawiki.org/wiki/API:Parsing_wikitext#parse) action___

### Step 2: Persistence in MongoDB

Now that you have some experience with the API and can sucessfully access
articles with associated metadata, it is time to start storing them in
[MongoDB](http://www.mongodb.org/)!

You should have a MongoDB [daemon](http://docs.mongodb.org/manual/tutorial
/manage-mongodb-processes/) running on your vagrant machine.  It is here that
you will be storing all of your data, but be aware of how many articles you are
crawling.

#### One article = ~120 kilobytes.  500MB / 120KB ≈ 4,250 articles.


```python
# import MongoDB modules
from pymongo import MongoClient
from bson.objectid import ObjectId

# connect to the hosted MongoDB instance
client = MongoClient('mongodb://localhost:27017/')

# connect to the wikipedia database: if it does not exist it will automatically create it -- one reason why mongoDB can be nice.
db = client.wikipedia
```

Each [database](http://docs.mongodb.org/manual/reference/glossary/#term-
database) has a number of
[collections](http://docs.mongodb.org/manual/reference/glossary/#term-
collection) analogous to SQL tables.  And each collection is comprised of
[documents](http://docs.mongodb.org/manual/reference/glossary/#term-document)
analogous to a rows in a SQL table.  And each document has
[fields](http://docs.mongodb.org/manual/reference/glossary/#term-field)
analogous to SQL columns.  Also, the docs have made a more comprehensive
[comparision](http://docs.mongodb.org/manual/reference/sql-comparison/).

![mongo_diagram](http://assets.zipfianacademy.com/data/images/mongo_diagram.png)


```python
# create a new collection that is unique to me, this is a necessity since we are all sharing a database 
collection = db.jdinu
```


```python
# try storing the document you retrieved earlier in MongoDB (be careful to not store duplicates!)
if not collection.find_one(r.json()['parse']):
    collection.insert(r.json()['parse'])
```


```python
# now see if you can query the database for the article you just stored
zipf = collection.find_one({ "title" : "Zipf's law"})
```

Now that you can store and retrieve articles in the Mongo Database, it is time
to iterate!

### Step 3: Retrieve and store every article (with associated metadata) within 1
hop from the 'Zipf's law' article.
<b style="color: red">Do not follow external links, only linked Wikipedia
articles</b>

___HINT: The Zipf's Law article should be located at: 'http://en.wikipedia.org/w
/api.php?action=parse&format=json&page=Zipf's%20law'___


```python
# grab the list of linked Wikipedia articles from the API result 
links = zipf['links']

# iterate over each link and store the returned document in MongoDB
for link in links:

    # parameters for API request
    payload = payload = { 'action' : 'parse' , 'format' : 'json','page' : link['*'] }
    
    r = requests.post(url, data=payload, headers=headers)
    
    # check to first see if the document is already in our database, if not... store it!
    if not collection.find_one(r.json()['parse']):
        collection.insert(r.json()['parse'])
```

### Step 3: Find all articles that mention 'Zipf' or 'Zipfian' (case
insensitive)

We will get some practice now with regular expressions in order to search the
content of the articles for the terms `Zipf` or `Zipfian`.  We only want
articles that mention these terms in the displayed text however, so we must
first remove all the unnecessary HTML tags and only keep what is in between the
relevant tags.  Beautiful Soup makes this almost trivial.  Explore the
documentation to find how to do this effortlessly: [http://www.crummy.com/softwa
re/BeautifulSoup/bs4/doc/](http://www.crummy.com/software/BeautifulSoup/bs4/doc/
)

Test out your Regular Expressions __before__ you run them over __every__
document you have in your database: [http://pythex.org/](http://pythex.org/).
Here is some useful documentation on regular expressions in Python: [http://docs
.python.org/2/howto/regex.html](http://docs.python.org/2/howto/regex.html)

Once you have identified the relevant articles, save them to a file for now, we
do not need to persist them in the database.

___HINT: There should be 10 articles___


```python
# import the Beautiful Soup module 
from bs4 import BeautifulSoup

count = 0

# compile our regular expression since we will use it many times
regex = re.compile('Zipf | Zipfian', re.IGNORECASE)

# create an output file if it doesn't already exist, and open it in binary mode
out = open('zipfian.txt', 'w+b')

# iterate over every document we have stored
for doc in collection.find():
    # extract the HTML from the document
    html = doc['text']['*']

    # stringify the ID for serialization to our text file
    doc['_id'] = str(doc['_id'])

    # create a Beautiful Soup object from the HTML
    soup = BeautifulSoup(html)

    # extract all the relevant text of the web page: strips out tags and head/meta content
    text = soup.get_text()

    # perform a regex search with the expression we compiled earlier
    m = regex.search(text)

    # if our search returned an object (it matched the regex), write the document to our output file
    if m:
        count += 1
        json.dump(doc, out) 
        out.write('\n')

# close the opened output file for good measure
out.close()
```


```python
count
```




    10



### Step 4: Augmentation!  Time to remix the web... or rather just Wikipedia.
But hey, isn't Wikipedia the web.

We want to augment our Zipfian Wikipedia articles with content from the __WWW__
at large.  Stepping out of the walled garden of collaboratively edited document
safety... let us scrape!  For each of the artcles we found to contain __'Zipf'__
or __'Zipfian'__, we want to know what the web has to say.  For each of the
___external links___ of said articles, fetch the linked webpage and extract the
__&lt;title&gt;__ and __&lt;meta name="keywords"&gt;__ from the __HTML__.
Beautiful Soup would probably help you a lot here.

___You still have to watch out for pages without keywords or a title___

Once you have extracted this information, update the stored document in your
database with this information. Add a field called __'extraexternal'__ that
contains the additional contextual information.  __'extraexternal'__ should be
an array of __JSON__ objects, each of which have keys:

* __'url'__ : the url of the page
* __'title'__ : the title of the page
* __'keywords'__ : the keywords from the meta tag

__Example:__

        {

         ...

         'displaytitle': "Zipf's law",
         'externallinks': [...],
         'text': {
                    '*': '<table class="infobox bordered" style="width:325px;
max-width:325px; font-size:95%; text-align: left;">\n<caption>Zipf\'s
law</caption>\n<tr...'
                  }
        'extraexternal' : [{
                             'url' : 'http://zipfianacademy.com',
                             'title' : 'Teaching the Long Tail | Zipfian
Academy'
                             'keywords' : 'data, datascience, science, bootcamp,
training, hadoop, big, bigdata, boot, camp, machine...'
                           }, ... ]
         ...
        }



```python
# re-open our output file of matched articles 
articles = open('zipfian.txt', 'r')

# iterate over each article that contains 'Zipf' or 'Zipfian'
for line in articles:
    doc = json.loads(line)

    # extract the external links from the Wikipedia article
    links = doc['externallinks']

    # deserialize our document ID into a Mongo ObjectID
    _id = ObjectId(doc['_id'])

    # create an empty 'extraexternal' array to store the results of our web scraping
    collection.update( { '_id' : _id }, { '$set' :  { 'extraexternal' : [] } } )

    # iterate over the URLs of the external links
    for url in links:
        # sometimes the URLs are malformed, split the URL into its component parts to fix
        scheme, netloc, path, qs, anchor = urlparse.urlsplit(url)

        # if there is not a scheme specified (what comes before the ://) default to HTTP
        scheme = scheme if scheme else 'http'

        # rejoin the fixed components into a URL string
        fixed_url = urlparse.urlunsplit((scheme, netloc, path, qs, anchor))

        # make the request to grab the content of the external link
        html = requests.get(fixed_url)

        # soupify the HTML so we can traverse/parse it for what we are looking for
        soup = BeautifulSoup(html.text)

        # extract the title from the page
        title = soup.title

        # extract the keywords from the meta tag
        keywords = soup.find('meta', attrs={'name' : 'keywords'})

        # create a object to store our extra data related to each external link
        augment = { 
                    'url' : url, 
                    'title' : title.string if title else "", 
                    'keywords' : keywords['content'].split(',') if keywords else [] 
                  }

        # update the document we already have stored in MongoDB with our additional information
        collection.update({ '_id' : _id }, { '$push' : { 'extraexternal' : augment } } )
```


```python
import pprint as pp

for doc in collection.find({'extraexternal' : { '$exists' :  True } }):
    pp.pprint(doc['title'])
    pp.pprint(doc['extraexternal'])
    print '\n'
```

    u'George Kingsley Zipf'
    [{u'keywords': [u'jan wassenaar',
                    u' 2dcurves',
                    u' mathematical curves',
                    u' hyperbola'],
      u'title': u'hyperbola',
      u'url': u'http://www.2dcurves.com/conicsection/conicsectionh.html'},
     {u'keywords': [],
      u'title': u'RAM-Verlag  \r\n\r\nRAM-Verlag Qantitative Linguistics',
      u'url': u'http://www.ram-verlag.de/g3inh.htm'},
     {u'keywords': [],
      u'title': u'\n    Zipf Dies After 3 - Month Illness |\n    \n        News |\n    \n    The Harvard Crimson\n',
      u'url': u'http://www.thecrimson.com/article.aspx?ref=204013'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://www.bibleplants.com/polaris/1919.htm#zipf'},
     {u'keywords': [],
      u'title': u'VIAF',
      u'url': u'http://viaf.org/viaf/79156155'}]
    
    
    u'Zeta distribution'
    [{u'keywords': [],
      u'title': u'',
      u'url': u'http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.66.3284&rep=rep1&type=pdf'},
     {u'keywords': [],
      u'title': u'Zipf Distribution -- from Wolfram MathWorld',
      u'url': u'http://mathworld.wolfram.com/ZipfDistribution.html'}]
    
    
    u'Brown Corpus'
    [{u'keywords': [],
      u'title': u'The Linguistics Encyclopedia - Google Books',
      u'url': u'http://books.google.com.au/books?id=IG7tE4-p-uUC&pg=PA87'},
     {u'keywords': [],
      u'title': u'Brown Corpus Manual',
      u'url': u'http://khnt.aksis.uib.no/icame/manuals/brown/'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://nltk.googlecode.com/svn/trunk/nltk_data/index.xml'},
     {u'keywords': [],
      u'title': u'The Brown Corpus Tag-set',
      u'url': u'http://www.scs.leeds.ac.uk/ccalas/tagsets/brown.html'},
     {u'keywords': [],
      u'title': u'Natural Language Toolkit \u2014 NLTK 2.0 documentation',
      u'url': u'http://www.nltk.org/'},
     {u'keywords': [],
      u'title': u'Part Of Speech Tagging - PHP/ir',
      u'url': u'http://phpir.com/part-of-speech-tagging'}]
    
    
    u'Principle of least effort'
    []
    
    
    u'Rank-size distribution'
    [{u'keywords': [],
      u'title': u'404 Not Found',
      u'url': u'http://people.few.eur.nl/vanmarrewijk/geography/zipf/'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://www-personal.umich.edu/~copyrght/image/monog08/fulltext.pdf'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://cep.lse.ac.uk/pubs/download/dp0641.pdf'},
     {u'keywords': [u''],
      u'title': u'Oxford University Press | Online Resource Centre | Online Resource Centres',
      u'url': u'http://www.oup.com/uk/orc/bin/9780199280988/01student/zipf/'},
     {u'keywords': [],
      u'title': u'The Return of Zipf: Towards a Further Understanding of the Rank-Size Distribution - Brakman - 2002 - Journal of Regional Science - Wiley Online Library',
      u'url': u'http://dx.doi.org/10.1111%2F1467-9787.00129'},
     {u'keywords': [],
      u'title': u'Rank-Size Distribution and the Process of Urban Growth ',
      u'url': u'http://dx.doi.org/10.1080%2F00420989550012960'},
     {u'keywords': [],
      u'title': u'\n The Pareto, Zipf and other power laws\n ',
      u'url': u'http://dx.doi.org/10.1016%2FS0165-1765(01)00524-9'},
     {u'keywords': [],
      u'title': u'Page not found - Mark Kimura, Ph.D. Official Site',
      u'url': u'http://www.mkimura.com/wikipedia/UseOfABM_RS.pdf'}]
    
    
    u'List of probability distributions'
    []
    
    
    u'Zipf\u2013Mandelbrot law'
    [{u'keywords': [],
      u'title': u'Introduction of relative abundance distribution (RAD) indices, estimated from the rank-frequency diagrams (RFD), to assess changes in community diversity',
      u'url': u'http://cat.inist.fr/?aModele=afficheN&cpsidt=1411186'},
     {u'keywords': [],
      u'title': u'Introduction of Relative Abundance Distribution (RAD) Indices, Estimated from the Rank-Frequency Diagrams (RFD), to Assess Changes in Community Diversity - Springer',
      u'url': u'http://dx.doi.org/10.1023%2FA:1006297211561'},
     {u'keywords': [],
      u'title': u'csKw:writings:computer:webcam',
      u'url': u'http://shaunwagner.com/writings_computer_evomus.html'},
     {u'keywords': [],
      u'title': u"[physics/9901035] Citations and the Zipf-Mandelbrot's law",
      u'url': u'http://arxiv.org/abs/physics/9901035'},
     {u'keywords': [u"Zipf's law"],
      u'title': u"Zipf's law",
      u'url': u'http://www.nist.gov/dads/HTML/zipfslaw.html'},
     {u'keywords': [],
      u'title': u'404 - Not Found',
      u'url': u'http://www.nslij-genetics.org/wli/zipf/index.html'},
     {u'keywords': [],
      u'title': u'Zipf Law Peculiarities for Dictionary Definitions',
      u'url': u'http://www.gelbukh.com/CV/Publications/2001/CICLing-2001-Zipf.htm'}]
    
    
    u'Pareto principle'
    [{u'keywords': [u'Juran  Joseph M',
                    u'Executives and Management',
                    u'Quality Control Handbook (Book)',
                    u'Deaths (Obituaries)',
                    u'New York State'],
      u'title': u'Joseph Juran, 103, Pioneer in Quality Control, Dies - New York Times',
      u'url': u'http://www.nytimes.com/2008/03/03/business/03juran.html'},
     {u'keywords': [u'Management Guru',
                    u' 80/20 rule',
                    u' Pareto\x92s law',
                    u' pareto principle',
                    u' the 80 20 principle',
                    u' effective speaking',
                    u' the 80 20 rule',
                    u' 80 20 principle',
                    u' 80 20 rule',
                    u' keynote address',
                    u' Consulting Service',
                    u' second opinion',
                    u' second opinion services',
                    u' Conference presentation',
                    u' Seminar presentation',
                    u' Report writing',
                    u' Written communication',
                    u' Presentation training',
                    u' Presentation skills',
                    u' Presentation skills training'],
      u'title': u'What is 80/20 Rule, Pareto\x92s Law, Pareto Principle,  The 80 20 Principle, Written communication, Presentation skills',
      u'url': u'http://www.80-20presentationrule.com/whatisrule.html'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://arxiv.org/PS_cache/cond-mat/pdf/0412/0412004v3.pdf'},
     {u'keywords': [u'The Forbes 100 billionaire rich list'],
      u'title': u'The Forbes top 100 billionaire rich-list  | This is Money',
      u'url': u'http://www.thisismoney.co.uk/news/article.html?in_article_id=418243&in_page_id=3'},
     {u'keywords': [u'human development report',
                    u' undp',
                    u' united nations development programme',
                    u' hdr',
                    u' capabilities approach',
                    u' nhdr',
                    u' hdi',
                    u' human development index',
                    u' gem',
                    u' gender empowerment measure',
                    u' gdi',
                    u' gender-related development index',
                    u' hpi',
                    u' human poverty index',
                    u' human development report office',
                    u' hdro',
                    u' un',
                    u' international development',
                    u' national development report',
                    u' regional development report',
                    u' journal of human development',
                    u' development paradigm',
                    u' homepage',
                    u' development policies',
                    u' development theory',
                    u' social development',
                    u' cultural development',
                    u' economic development',
                    u' economic growth theory',
                    u' social progress',
                    u' social justice',
                    u' development as freedom',
                    u' human rights approach',
                    u' capability approach',
                    u' human development approach',
                    u' amartya sen',
                    u' mahbub ul haq',
                    u' human mobility',
                    u' fighting climate change',
                    u' water scarcity',
                    u' Aid trade and security',
                    u' human security',
                    u' Millennium Development Goals',
                    u' MDGs',
                    u' gender inequality',
                    u' citizen s participation in development',
                    u' participatory development',
                    u' participatory approach',
                    u' National and international strategies for development',
                    u' Concepts and measurements of development',
                    u' national statistics',
                    u' global statistics',
                    u' measuring development',
                    u' measuring progress',
                    u' statistical data',
                    u''],
      u'title': u'Reports (1990-2013) | Global Reports | HDR 1992 | Download | Human Development Reports (HDR) | United Nations Development Programme (UNDP)',
      u'url': u'http://hdr.undp.org/en/reports/global/hdr1992/chapters/'},
     {u'keywords': [u'.NET ', u' Security'],
      u'title': u"Microsoft's CEO: 80-20 Rule Applies To Bugs, Not Just Features",
      u'url': u'http://www.crn.com/news/security/18821726/microsofts-ceo-80-20-rule-applies-to-bugs-not-just-features.htm'},
     {u'keywords': [u''],
      u'title': u'\n\nKathryn Woodcock - Ryerson University\n',
      u'url': u'http://www.ryerson.ca/woodcock/'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://www.uscg.mil/hq/cg5/cg5211/docs/RBDM_Files/PDF/RBDM_Guidelines/Volume%202/Volume%202-Chapter%206.pdf'},
     {u'keywords': [],
      u'title': u'Growing artificial societies [electronic resource]: social science from the ... - Joshua M. Epstein - Google Books',
      u'url': u'http://books.google.com/?id=xXvelSs2caQC'},
     {u'keywords': [u'Providence',
                    u' Breaking News',
                    u' RI Talks',
                    u' Blogs',
                    u' Education',
                    u' Environment',
                    u' Health',
                    u' Police & Fire',
                    u' Politics',
                    u' Sports',
                    u' Business',
                    u' Arts',
                    u' Entertainment',
                    u' Events',
                    u' Cars',
                    u' Real Estate',
                    u' Jobs',
                    u' Obituaries',
                    u' Weather',
                    u' Traffic',
                    u' Markets',
                    u' Lotteries'],
      u'title': u'\nProvidence Journal | Rhode Island news, sports, weather & more - The Providence Journal\n',
      u'url': u'http://www.projo.com/opinion/contributors/content/CT_weinberg27_07-27-09_HQF0P1E_v15.3f89889.html'},
     {u'keywords': [u'girls',
                    u' chubby bunny',
                    u' marshmallows',
                    u' cute',
                    u' pretty',
                    u' adorable',
                    u' funny girls',
                    u' crazy girls'],
      u'title': u'three cute girls do the chubby bunny challenge - YouTube',
      u'url': u'http://howdoigetoffdrugs.com/2010/01/career-criminals-who-are-they-and-what-should-society-do-about-them/'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://www.dmacorporation.com/dmarecognized/MortgagePress.pdf'},
     {u'keywords': [],
      u'title': u'On Line Calculator: Inequality',
      u'url': u'http://www.poorcity.richcity.org/calculator/?quantiles=82.4,17.6%7C17.6,82.4'},
     {u'keywords': [],
      u'title': u'Informetric distributions, part I: Unified overview - Bookstein - 1999 - Journal of the American Society for Information Science - Wiley Online Library',
      u'url': u'http://dx.doi.org/10.1002%2F(SICI)1097-4571(199007)41:5%3C368::AID-ASI8%3E3.0.CO%3B2-C'},
     {u'keywords': [],
      u'title': u'\n The Forbes 400 and the Pareto wealth distribution\n ',
      u'url': u'http://dx.doi.org/10.1016%2Fj.econlet.2005.08.020'},
     {u'keywords': [u'Business',
                    u' success',
                    u' pareto',
                    u' principle',
                    u' 8020',
                    u' businesseconomics'],
      u'title': u'The 80-20 Principle The Secret to Success by Achieving More with Less',
      u'url': u'http://www.scribd.com/doc/3664882/The-8020-Principle-The-Secret-to-Success-by-Achieving-More-with-Less'},
     {u'keywords': [],
      u'title': u'\n The Pareto, Zipf and other power laws\n ',
      u'url': u'http://dx.doi.org/10.1016%2FS0165-1765(01)00524-9'},
     {u'keywords': [],
      u'title': u'\n The size distribution of cities: An examination of the Pareto law and primacy\n ',
      u'url': u'http://dx.doi.org/10.1016%2F0094-1190(80)90043-1'},
     {u'keywords': [u'pareto principle principal law 80/20 80-20 80 20 rule dr joseph juran vital few and trivial many superstar management theory'],
      u'title': u"Pareto's Principle - The 80-20 Rule",
      u'url': u'http://management.about.com/cs/generalmanagement/a/Pareto081202.htm'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://web.archive.org/web/20080528063231/http://www.ma.hw.ac.uk/~des/HWM00-26.pdf'}]
    
    
    u"Zipf's law"
    [{u'keywords': [],
      u'title': u'Zipf, Power-law, Pareto - a ranking tutorial',
      u'url': u'http://www.hpl.hp.com/research/idl/papers/ranking/ranking.html'},
     {u'keywords': [],
      u'title': u'404 - Not Found',
      u'url': u'http://www.nslij-genetics.org/wli/pub/ieee92_pre.pdf'},
     {u'keywords': [],
      u'title': u'IEEE Xplore - \r\n\t\tRandom texts exhibit Zipf&#39;s-law-like word frequency distribution\r\n\t',
      u'url': u'http://dx.doi.org/10.1109%2F18.165464'},
     {u'keywords': [],
      u'title': u"Peter Neumann's Home Page",
      u'url': u'http://www.csl.sri.com/users/neumann/#12a'},
     {u'keywords': [],
      u'title': u'WebCite query result',
      u'url': u'http://www.webcitation.org/5z2UByabR'},
     {u'keywords': [],
      u'title': u'Least effort and the origins of scaling in human language ',
      u'url': u'http://www.pnas.org/content/100/3/788.abstract?sid=cc7fae18-87c9-4b67-863a-4195bb47c1d1'},
     {u'keywords': [],
      u'title': u'Least effort and the origins of scaling in human language ',
      u'url': u'http://dx.doi.org/10.1073%2Fpnas.0335980100'},
     {u'keywords': [],
      u'title': u'Least effort and the origins of scaling in human language',
      u'url': u'//www.ncbi.nlm.nih.gov/pmc/articles/PMC298679'},
     {u'keywords': [u'PubMed',
                    u' National Center for Biotechnology Information',
                    u' NCBI',
                    u' United States National Library of Medicine',
                    u' NLM',
                    u' MEDLINE',
                    u' Medical Journals',
                    u' pub med',
                    u' Entrez',
                    u' Journal Articles',
                    u' Citation search'],
      u'title': u'Least effort and the origins of sca... [Proc Natl Acad Sci U S A. 2003] - PubMed - NCBI',
      u'url': u'//www.ncbi.nlm.nih.gov/pubmed/12540826'},
     {u'keywords': [u'factorial randomness',
                    u" Zipf's Law",
                    u" Benford's Law",
                    u' factors',
                    u' natural numbers',
                    u' random',
                    u' randomness',
                    u' chi-square',
                    u' linear regression',
                    u' PASCAL',
                    u' lemma',
                    u' correlation coefficient',
                    u' benford',
                    u' zipf'],
      u'title': u'Factorial Randomness',
      u'url': u'http://home.zonnet.nl/galien8/factor/factor.html'},
     {u'keywords': [],
      u'title': u'Zipf Law Peculiarities for Dictionary Definitions',
      u'url': u'http://www.gelbukh.com/CV/Publications/2001/CICLing-2001-Zipf.htm'},
     {u'keywords': [],
      u'title': u"[cs/0406015] Zipf's law and the creation of musical context",
      u'url': u'http://xxx.arxiv.org/abs/cs.CL/0406015'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://pages.stern.nyu.edu/~xgabaix/papers/zipf.pdf'},
     {u'keywords': [],
      u'title': u"Zipf's Law for Cities: An Explanation ",
      u'url': u'http://dx.doi.org/10.1162%2F003355399556133'},
     {u'keywords': [u'Makro\xc3\x83\xc2\xb8konomi',
                    u' mikro\xc3\x83\xc2\xb8konomi',
                    u' makro\xc3\x83\xc2\xb8konomiske tidsskrifter.'],
      u'title': u'Quarterly journal of economics. (eJournal / eMagazine) [WorldCat.org]',
      u'url': u'//www.worldcat.org/issn/0033-5533'},
     {u'keywords': [u''],
      u'title': u'Guest Column: Math and the City - NYTimes.com',
      u'url': u'http://judson.blogs.nytimes.com/2009/05/19/math-and-the-city/'},
     {u'keywords': [u'The Atlantic',
                    u' The Atlantic Magazine',
                    u' TheAtlantic.com',
                    u' Atlantic',
                    u' news',
                    u' opinion',
                    u' breaking news',
                    u' analysis',
                    u' commentary',
                    u' business',
                    u' politics',
                    u' culture',
                    u' international',
                    u' science',
                    u' technology',
                    u' national and life'],
      u'title': u'Seeing Around Corners - Jonathan Rauch - The Atlantic',
      u'url': u'http://www.theatlantic.com/issues/2002/04/rauch.htm'},
     {u'keywords': [],
      u'title': u"Zipf's law | planetmath.org",
      u'url': u'http://planetmath.org/encyclopedia/ZipfsLaw.html'},
     {u'keywords': [],
      u'title': u'Comptes Rendus de l\x92Acad\xe9mie des Sciences',
      u'url': u'http://www.hubbertpeak.com/laherrere/fractal.htm'},
     {u'keywords': [],
      u'title': u'Sign in to read: Why it is hard to share the wealth - physics-math - 12 March 2005 - New Scientist',
      u'url': u'http://www.newscientist.com/article.ns?id=mg18524904.300'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://www.lexique.org/listes/liste_mots.txt'},
     {u'keywords': [u'mobile',
                    u' pi theorem',
                    u' php',
                    u' ajax',
                    u' semantic web',
                    u' india',
                    u' philosophy'],
      u'title': u'Semantic Depth Analyzer',
      u'url': u'http://1.1o1.in/en/webtools/semantic-depth'},
     {u'keywords': [],
      u'title': u"[physics/9901035] Citations and the Zipf-Mandelbrot's law",
      u'url': u'http://uk.arxiv.org/abs/physics/9901035'},
     {u'keywords': [],
      u'title': u'Wolfram Demonstrations Project',
      u'url': u'http://demonstrations.wolfram.com/ZipfsLawForUSCities/'},
     {u'keywords': [],
      u'title': u"Zipf's Law -- from Wolfram MathWorld",
      u'url': u'http://mathworld.wolfram.com/ZipfsLaw.html'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://www.geoffkirby.co.uk/ZIPFSLAW.pdf'},
     {u'keywords': [u' Mathematics', u' Statistics', u' Physics'],
      u'title': u"\n\t\t\tComplex systems: Unzipping Zipf's law :  Nature :  Nature Publishing Group\n\t\t",
      u'url': u'http://www.nature.com/nature/journal/v474/n7350/full/474164a.html'},
     {u'keywords': [],
      u'title': u'CiteSeerX',
      u'url': u'http://citeseer.ist.psu.edu/context/64879/0'}]
    
    
    u'Stable distribution'
    [{u'keywords': [],
      u'title': u'Generalization of symmetric \u03b1-stable L\xe9vy distributions for q>1',
      u'url': u'http://www.ncbi.nlm.nih.gov/pmc/articles/PMC2869267'},
     {u'keywords': [],
      u'title': u"[0911.2009] Generalization of symmetric $\\alpha$-stable L\\'evy distributions for\n  $q&gt;1$",
      u'url': u'http://arxiv.org/abs/0911.2009'},
     {u'keywords': [],
      u'title': u'Generalization of symmetric alpha-stable L\xe9vy distributions for q>1',
      u'url': u'http://adsabs.harvard.edu/abs/2010JMP....51c3502U'},
     {u'keywords': [],
      u'title': u'Generalization of symmetric \u03b1-stable L\xe9vy distributions for q>1 | J. Math. Phys. - Journal of Mathematical Physics',
      u'url': u'http://dx.doi.org/10.1063%2F1.3305292'},
     {u'keywords': [],
      u'title': u'Generalization of symmetric \u03b1-stable L\xe9vy distributions for q>1',
      u'url': u'//www.ncbi.nlm.nih.gov/pmc/articles/PMC2869267'},
     {u'keywords': [u'PubMed',
                    u' National Center for Biotechnology Information',
                    u' NCBI',
                    u' United States National Library of Medicine',
                    u' NLM',
                    u' MEDLINE',
                    u' Medical Journals',
                    u' pub med',
                    u' Entrez',
                    u' Journal Articles',
                    u' Citation search'],
      u'title': u'Generalization of symmetric alpha-stable L\xe9vy di... [J Math Phys. 2010] - PubMed - NCBI',
      u'url': u'//www.ncbi.nlm.nih.gov/pubmed/20596232'},
     {u'keywords': [],
      u'title': u'\n        Taylor & Francis Online\n         :: Theory of the pressure broadening and shift of spectral lines - Advances in Physics - Volume 30,\n        Issue 3 \n    ',
      u'url': u'http://journalsonline.tandf.co.uk/openurl.asp?genre=article&eissn=1460-6976&volume=30&issue=3&spage=367'},
     {u'keywords': [],
      u'title': u'Theory of the pressure broadening and shift of spectral lines.',
      u'url': u'http://adsabs.harvard.edu/abs/1981AdPhy..30..367P'},
     {u'keywords': [],
      u'title': u'\n        Taylor & Francis Online\n         :: Theory of the pressure broadening and shift of spectral lines - Advances in Physics - Volume 30,\n        Issue 3 \n    ',
      u'url': u'http://dx.doi.org/10.1080%2F00018738100101467'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://www.fel.duke.edu/~scafetta/pdf/opinion0308.pdf'},
     {u'keywords': [],
      u'title': u'Page not found (Error 404) | University of North Texas Libraries',
      u'url': u'http://www.library.unt.edu/theses/open/20013/leddon_deborah/dissertation.pdf'},
     {u'keywords': [u'one-dimensional strongly stable laws',
                    u' multivariate spherically symmetric stable distributions',
                    u' higher transcendental functions',
                    u' generalized hypergeometrical series',
                    u' Meijer\u2019s G-functions'],
      u'title': u'\n    \n            On Representation of Densities of Stable Laws by Special Functions : Theory of Probability & Its Applications: Vol. 39, No. 2\n        \n    (Society for Industrial and Applied Mathematics)\n',
      u'url': u'http://epubs.siam.org/tvp/resource/1/tprbau/v39/i2/p354_s1'},
     {u'keywords': [u'one-dimensional strongly stable laws',
                    u' multivariate spherically symmetric stable distributions',
                    u' higher transcendental functions',
                    u' generalized hypergeometrical series',
                    u' Meijer\u2019s G-functions'],
      u'title': u'\n    \n            On Representation of Densities of Stable Laws by Special Functions : Theory of Probability & Its Applications: Vol. 39, No. 2\n        \n    (Society for Industrial and Applied Mathematics)\n',
      u'url': u'http://dx.doi.org/10.1137%2F1139025'},
     {u'keywords': [],
      u'title': u'Continuous and discrete properties of stochastic processes - Nottingham eTheses',
      u'url': u'http://etheses.nottingham.ac.uk/1194/'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://academic2.american.edu/~jpnolan/stable/chap1.pdf'},
     {u'keywords': [],
      u'title': u'',
      u'url': u'http://arxiv.org/pdf/math/0408321v2'},
     {u'keywords': [],
      u'title': u'\nWiley: The Best of Wilmott 1: Incorporating the Quantitative Finance Review - Paul Wilmott\n\n   ',
      u'url': u'http://www.wiley.com/WileyCDA/WileyTitle/productCd-0470023511,descCd-tableOfContents.html'},
     {u'keywords': [],
      u'title': u'Page not found | planetmath.org',
      u'url': u'http://planetmath.org/encyclopedia/StrictlyStableRandomVariable.html'},
     {u'keywords': [u'stable distribution',
                    u' heavy tails',
                    u' pareto',
                    u' stable law'],
      u'title': u"John Nolan's Stable Distribution Page",
      u'url': u'http://academic2.american.edu/~jpnolan/stable/stable.html'},
     {u'keywords': [],
      u'title': u'\r\n  Overview\r\n ',
      u'url': u'http://www.mathestate.com/tools/Financial/map/Overview.html'}]
    
    


# Congratulations!

![won-the-
internet](http://25.media.tumblr.com/tumblr_m8bg80KH5l1qlh1s6o1_400.gif)

#### You have made it to the end (hopefully succcessfully).  Now that you have
your data and have contextualized it with information from the web, you can
start performing some interesting analyses on it. Some ideas to get you started:

* [Naive Bayes](https://en.wikipedia.org/wiki/Naive_Bayes_classifier#Document_Cl
assification) document clasification using [bag of
words](http://en.wikipedia.org/wiki/Bag-of-words_model) vectorization
* [Six Degrees of
Wikipedia](http://en.wikipedia.org/wiki/Wikipedia:Six_degrees_of_Wikipedia)
graph traversal
* Other awesome things!


[3]: http://www.infoq.com/resource/articles/mongodb-java-php-python/en/resources/non-sharded-client-connection.png
[3.1]: http://stackoverflow.com/a/7287459
[4]: http://docs.mongodb.org/ecosystem/drivers/
[5]: http://assets.zipfianacademy.com/data/images/mongo_diagram.png
[6]: http://docs.mongodb.org/manual/tutorial/getting-started/
[7]: https://github.com/variety/variety
