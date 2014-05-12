## Overview

We will be using the NYT API to download articles from the web.  Eventually we will be performing NLP and machine learning on the articles to do awesome things like document clustering, document filtering/classification, network analysis, and some indexing.  For this sprint we are just concerned with getting some data.

## Goals (part 1)
* HTTP and hypermedia (i.e. links)
* ReST
* MongoDB
* JSON APIs
* Beautiful Soup and Requests libraries
* HTML and CSS
* Data "Join" (i.e. get metadata from API and combine that with web scraping)
* Crawling vs. Parsing

## Goals (part 2)

* SQL
    * INSERT
    * CREATE table
    * schemas
    * indices
    * SELECT and WHERE
    * joins
* Tokenization
* Vectorization -- tokens => numbers
* Sparse Matrix 
* Feature hashing
* Bag of words
* tf-idf
* Ngrams
* Normalization/feature scaling


## Reading

* [How does the internet work](http://docs.webplatform.org/wiki/concepts/internet_and_web/how_does_the_internet_work)

* Data Science Rules: Web Scraping and HTML
   * [Part 1](http://datasciencerules.blogspot.com/2012/11/web-scraping-and-html-parsing.html)
   * [Part 2](http://datasciencerules.blogspot.com/2012/11/web-scraping-and-html-parsing-2.html)

## Assignment

### Outline

* __Get experience using an [API](http://en.wikipedia.org/wiki/Application_programming_interface) to access the [NYT](http://www.nytimes.com/) articles__
* __Store the retrieved articles and metadata in [MongoDB](http://www.mongodb.org/)__ 
* __Use [regular expressions](http://en.wikipedia.org/wiki/Regular_expression) in [Python](http://docs.python.org/2/howto/regex.html) to search for all articles that contain the word 'Zipf' or 'Zipfian'__
* __Have [FUN!](http://media.giphy.com/media/LlmVkDId8FzP2/giphy.gif) (that's an order)__

## Exercise Part 1

For this exercise we will be using the NYT [API](http://developer.nytimes.com/docs/read/article_search_api_v2) to programmatically retrieve its articles.

1. Obtain an API key from the NYT for the Article Search API: [http://developer.nytimes.com/apps/register](http://developer.nytimes.com/apps/register)
2. Now that we have access we can begin to have some fun!  Make a request to the article search API endpoint to retrieve the articles for last week.  
    * Look for what [parameters](http://developer.nytimes.com/docs/read/article_search_api_v2) you can set in your request using the API
    * You probably want to use a HTTP library.  I like [Requests](http://docs.python-requests.org/en/latest/) but you can use whichever you like.
3. Examine one of the articles returned.  Look at it's structure and the fields returned.  Make sure you can get a single article (and you know what it looks like) before you retrieve __ALL__ of the NYT.
4. Now that you have some experience with the API and can sucessfully access articles with associated metadata, it is time to start storing them in [MongoDB](http://www.mongodb.org/)!

### MongoDB interlude

You should have a MongoDB [daemon](http://docs.mongodb.org/manual/tutorial/manage-mongodb-processes/) running on your vagrant machine.  It is here that you will be storing all of your data, but be aware of how many articles you are crawling.

Each [database](http://docs.mongodb.org/manual/reference/glossary/#term-database) has a number of [collections](http://docs.mongodb.org/manual/reference/glossary/#term-collection) analogous to SQL tables.  And each collection is comprised of [documents](http://docs.mongodb.org/manual/reference/glossary/#term-document) analogous to a rows in a SQL table.  And each document has [fields](http://docs.mongodb.org/manual/reference/glossary/#term-field) analogous to SQL columns.  Also, the docs have made a more comprehensive [comparison](http://docs.mongodb.org/manual/reference/sql-comparison/).

![mongo_diagram](http://zipfianacademy.com/data/images/mongo_diagram.png)

1. Try storing the document you retrieved earlier in MongoDB (be careful to not store duplicates!).  We will be using the [pymongo](http://api.mongodb.org/python/current/tutorial.html) library to interface to MongoDB from Python.
2. Now see if you can query the database for the article you just stored.
3. Now that you can store and retrieve articles in the Mongo Database, it is time to iterate!

### Scaling up

4. Now that you have a sense of what a single article response looks like, we can begin to scale up.  Begin downloading all of the NYT articles starting with the most recent.  You will not have time (or effort) to download all of the corpus, so let us start with the most recent articles and download as many as we can!  Store these in mongoDB.
5.  Inspect how many articles were returned from your request for all of the NYT.  
    * How many documents are there?  
    * How many total articles are there in all of the NYT?  
    * Look to the API docs for the Article Search API to learn about why there is a discrepancy between these two numbers (and how to find the second number -- total articles)
6. The NYT does some things to prevent us from getting it's articles.  But we are clever data scientists (cleverer than the NYT API that is)!
    * Find out how to circumvent the API pagination.
    * The NYT is rate limited.  Deal with it.
    * The API only lets you access a fixed number of pages (from the pagination).  What is this number and how can you get around this limit?
7. Once you have figured how to deal with the NYT quirks, we are ready to start looping.  Begin to download the 10,000 most recent NYT articles.
    * You will want to check how your loop is progressing, be sure to print some checkpoint information on how many documents it has downloaded (maybe every 100 articles?).  

### Where are we 

* We have successful gathered article metadata from the NYT API
* We have stored said data in MongoDB
* We have URLs for each article that we can now use for scraping

8. Do you remember James Cropcho of Data Science Pyramid fame?  Well he coincidentally is also James Cropcho of [Variety](http://blog.mongodb.org/post/21923016898/meet-variety-a-schema-analyzer-for-mongodb) fame!  Use the Variety [library](https://github.com/variety/variety) to analyze the returned documents.
    * How many have a headline?  What about a Headline kicker?
    * How many have multimedia?
    * What about a web URL? Do all of them?  I bet articles published before 1991 are less likely to have a web URL.

### Step 4: Augmentation!  Time to remix the web...

![magritte_remix](http://25.media.tumblr.com/0b608535743de2928ea2c3a76b771fcd/tumblr_mogx75qjxQ1qedb29o1_500.gif)

9. Now that we have all the meta data, it it time to get the article content!  We will be doing something I call a data join (some people call it [data blending](http://www.tableausoftware.com/videos/data-integration)... but they charge you money so they can call it that).
    * Iterate over your collection in your database.  For all the articles for which you do not have HTML content (this will be all of them to begin with), use the 'web url' in the meta data to make a web request.
    * Use Beautiful Soup to parse the returned HTML.
    * Augment your meta data in the database with this raw HTML from the web page.
    * The web page has much more than just the article content.  Find out how to extract just the article body and store this in the database as well.
    * Store __both__ the raw HTML and the article content in the database.

### Congratulations!

![won-the-internet](http://25.media.tumblr.com/tumblr_m8bg80KH5l1qlh1s6o1_400.gif)

You have made it to the end (hopefully succcessfully).  Now that you have your data and have contextualized it with information from the web, you can start performing some interesting analyses on it. Some ideas to get you started:

## Exercise Part 2

We are beginning our descent into natural language processing and machine learning.  You have learned to work with APIs.  You have learned how to scrape the web.  You have learned how to search text.  Now it is time to take things to the next level!  Before we can start classifying text, clustering documents, and discovering topics we need to turn all these messy words into clean numbers.  Feature extraction/engineering is one of the more subjective aspects of ML and often depends on experimentation and domain knowledge.  It is here that we will learn its art.  And also learn SQL along the way ;)

![og](http://image.slidesharecdn.com/textclassificationscikit-learnpycon2011ogrisel-110311132018-phpapp01/95/slide-14-728.jpg?1299871296)

### Outline

1. Create SQL tables
2. Parse articles: single string of text => tokenized words
3. Index words + documents into SQL tables
4. Using SQL, calculate bag-of-words vectors for each document
5. Apply tf-idf weighting to feature vectors

__HTML -> Text -> Tokens -> bag-of-words vector -> tf-idf -> ALGORITHMS! -> $$$__

### Tokenization

1. The first step is to setup some SQL tables to store index our words into.  We will be using [SQLite](http://www.sqlite.org) and the [pysqlite](https://code.google.com/p/pysqlite/) library to make queries from python.  Read the [docs](https://pysqlite.readthedocs.org/en/latest/sqlite3.html) to learn how to create a 'connection' (remember SQLite stores its database as a file) to a database and issue queries.  We will be creating the following tables (the same as p. 59 of "Collective Intelligence") and setting up our schema:

__urls__

| id | url |
| :--:| :--|
| 1 | "http://nyt.com/hotdogs_in_the_park..."|
| 2 | ...|
| ... | ...| 

__wordlist__

| id | word | position|
| :--:| :--| :-- |
| 1 | "dog"| 5 |
| 2 | "car" | 12453 |
| ... | ...| ... |

__wordlocation__

| id | url_id | word_id | location|
| :--:| :--| :--| :--|
| 1 | 54 | 2 | 523 |
| 2 | 1 | 1 | 12 |
| ... | ...| ... | ...|

__urls table: mapping of (url => url_id)__

__wordlist table: mapping of (word => word_id, position/index) in feature vector__

__wordlocation table: connect words => urls. (location is position of word in article, i.e. "dog" is the 12th word in "http://nyt.com/hotdogs_in_the_park...")__

3. Also create an [index](http://www.sqlite.org/lang_createindex.html) for these 3 tables.  

4. Tokenization!  Tokenization is not an exact science.  Do you split on whitespace? Punctuation? What about URLs (and words with weird symbols)?  Read about [tokenization](http://nltk.org/book/ch03.html) and use NLTK to tokenize the documents using regular expressions.  You can test out various regular expressions using this handy site: [https://pythex.org/](https://pythex.org/) and the [NTLK tokenizer docs](http://nltk.org/api/nltk.tokenize.html)

5. Now that we have tokenized things, we can index!  Before we index however, let us get rid of punctuation and [stop words](http://nltk.org/book/ch02.html).  Filter out any punctuation and stop words from the tokenization process (if there is any).  Also, normalize and lemmatize the text.

* convert all words to lowercase
* [Lemmatize the text](http://nltk.org/book/ch03.html)

6. Index the words for each document into the appropriate tables.  The order of the list of tokens should preserve the order of the content from the HTML.  Use this fact to build `wordlocation`.  Also there is something called a [foreign key](http://www.sqlite.org/foreignkeys.html) that you will use to link the rows in the tables together.

for each document:
* Insert the associated URL into the `url` table.  Remember the id it was inserted with, you will need this for your foreign keys.
* Insert each word into the `wordlist` table.  Remember this id as well.
* Insert the word location in the document, and the two ids from the previous inserts to link the tables.

7. Yay!  We now have a text index of (part of) the NYT.  Now lets put those words into bags!  In a [bag-of-words](http://en.wikipedia.org/wiki/Bag-of-words_model) model, each word has a unique index in the feature vector.  This vector will be as wide as our corpus (i.e. the English language).  In order to implement this in SQL, we will not need to actually create a separate (and VERY wide) table.  This would be somewhat bad.  Instead we will use our `wordlist` table.  Its second column appropriate maps each word to a position in our feature vectors.  Run through the NLTK [Brown corpus](http://nltk.org/book/ch02.html) and add anywords not already in our `wordlist` table to it and assign each a position for our feature vector.

8. For our bag-of-words (and tf-idf) we will be calculating them using our tables.  Bag-of-words should be quite simple now that we have indexed our words.  [Join](http://zetcode.com/db/sqlite/joins/) (INNNER should be fine) `wordlocation` and `wordlist` to link a url_id with the words the article contains.  We can [GROUP BY](http://www.tutorialspoint.com/sqlite/sqlite_group_by.htm) `url_id` and `word`.  Do a COUNT on this group to create a [VIEW](http://zetcode.com/db/sqlite/viewstriggerstransactions/) of our bag of words.  Add an additional column of the count of each word.  This view should have columns for: word_id, url_id, and count.  This view is our bag.

9. [tf-idf](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) is a short hop away from a bag.  We basically need to update our counts based on the total occurence of words across all articles.  I will leave it up to you to use SQL aggregates and the method we have used above to create a view for tf-idf weighting as well. 

![tf](https://upload.wikimedia.org/math/5/c/c/5cc18acd4dbd9be636047fc2a7a10105.png)

![idf](https://upload.wikimedia.org/math/6/9/1/691e665ba9ae9448219cedb8365bf961.png)

## Extra credit

* [Feature Hashing](http://en.wikipedia.org/wiki/Feature_hashing)
* [Normalization/Feature Scaling](http://en.wikipedia.org/wiki/Feature_scaling)
* Additional Features: total word count, sentiment of article, metadata of article, etc.

## Resources
### HTTP

* [HTTP Request Life Cycle (StackOverflow)](http://stackoverflow.com/questions/4814514/http-request-life-cycle)


### ReST
* [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer)
* [Haters gonna HATEOAS](http://timelessrepo.com/haters-gonna-hateoas)

## Resources

* [Requests HTTP library](http://docs.python-requests.org/en/latest/)
* [Regular expression tester](http://pythex.org/)
* [Google Regex tutorial](https://developers.google.com/edu/python/regular-expressions)
* [Beautiful Soup (HTML parsing and searching)](http://www.crummy.com/software/BeautifulSoup/)
* [MongoDB Python driver](http://api.mongodb.org/python/current/tutorial.html)
* [Fielding's Dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
* [List of common HTTP status codes](http://www.smartlabsoftware.com/ref/http-status-codes.htm)
* [API client](http://www.getpostman.com/)
* [What is a "protocol"?](http://thomaslevine.com/!/street-sign-protocol)
* [Web Scraping Tools](http://blog.marcua.net/post/74655674340)

* __[Requests HTTP library](http://docs.python-requests.org/en/latest/)__
* __[wget](http://www.gnu.org/software/wget/)__ is your friend. __[curl](http://curl.haxx.se/)__ is your homie. 
* __[Regular expression tester](http://pythex.org/)__
* __[Google Regex tutorial](https://developers.google.com/edu/python/regular-expressions)__
* __[Beautiful Soup (HTML parsing and searching)](http://www.crummy.com/software/BeautifulSoup/)__
* __[MongoDB Python driver](http://api.mongodb.org/python/current/tutorial.html)__
* __[Variety](https://github.com/variety/variety)__ Schema Analyzer
* __[boilerpipe](https://github.com/misja/python-boilerpipe)__ article extraction
* Programming Collective Intelligence (Python): Chapter 4 (p.54 - p.69)
* Programming Collective Intelligence (Python): Chapter 6
* Machine Learning in Action (Python): Chapter 4.5
* Machine Learning for Hackers (R): Chapter 3
* [NLTK Book: 3.7 Toeknizing Text](http://nltk.org/book/ch03.html)
* [Text classification with NLTK and scikit-learn (slides)](http://www.slideshare.net/ogrisel/statistical-machine-learning-for-text-classification-with-scikitlearn-and-nltk)
* [scikit-learn: Text Feature extraction](http://scikit-learn.org/stable/modules/feature_extraction.html#text-feature-extraction)
* [Finding Important words with tf-idf](http://www.stevenloria.com/finding-important-words-in-a-document-using-tf-idf/)
* [NLTK book](http://nltk.org/book/)
* [Hashing language](http://blog.someben.com/2013/01/hashing-lang/)
* [SQL examples](http://www.thegeekstuff.com/2012/09/sqlite-command-examples/)
* [Learn SQL the hard way](http://sql.learncodethehardway.org/book/)

## Data Sources (ranked by ease of use... usually)

### DaaS -- Data as a service 
* time series: [Quandl](http://www.quandl.com/)
* public datasets: [enigma](http://enigma.io/)
* location contextualization: [factual](http://www.factual.com/)
* financial modeling: [Quantopian](https://www.quantopian.com/)
* email contextualization: [Rapleaf](http://www.rapleaf.com/why-rapleaf/)
* social media: [Gnip](http://gnip.com/)
        
### Bulk Downloads -- just like the good ol' days

* FTP servers
* Amazon S3 public [datasets](http://aws.amazon.com/publicdatasets/)
* [InfoChimps](http://www.infochimps.com/datasets)
* Academia -- [Stanford](http://snap.stanford.edu/data/) and [UCI](http://archive.ics.uci.edu/ml/)

### APIs -- public and hidden
* [Twitter](https://dev.twitter.com/)
* [Foursquare](https://developer.foursquare.com/)
* [Facebook](https://developers.facebook.com/docs/reference/apis/)
* [Tumblr](http://www.tumblr.com/docs/en/api/v2)
* [Rdio](http://developer.rdio.com/)
* [Yelp](http://www.yelp.com/developers/documentation)
* [Last.fm](http://www.last.fm/api)
* [bitly](http://dev.bitly.com/)
* [LinkedIn](https://developer.linkedin.com/apis)
* [Yahoo Finance (hidden)](http://greenido.wordpress.com/2009/12/22/yahoo-finance-hidden-api/)
* [etc.](http://developer.trulia.com/)
* [etc.](http://dev.evernote.com/documentation/cloud/)
* [etc.](http://www.songkick.com/developer/)

### DIY
* Webscraping 
* manual downloads

### If you have any other favorite datasources, please post them to [Piazza](https://piazza.com/class/hkpmwswpcjcxq)!

### Tools to store the Data

* Relational Database: Postgres and MySQL 
* NoSQL: Document (MongoDB, CouchDB), Key-Value (Redis), Distributed (HDFS, Riak, Cassandra)
* Text Files (never hurt anyone)

## Glossary

* tokenization
* feature vector
* tables, schemas, foreign keys, and primary keys 
