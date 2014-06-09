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

## Key Reading

* [How does the internet work](http://docs.webplatform.org/wiki/concepts/internet_and_web/how_does_the_internet_work)
* Data Science Rules: Web Scraping and HTML
   * [Part 1](http://datasciencerules.blogspot.com/2012/11/web-scraping-and-html-parsing.html)
   * [Part 2](http://datasciencerules.blogspot.com/2012/11/web-scraping-and-html-parsing-2.html)
* [HTTP Request Life Cycle (StackOverflow)](http://stackoverflow.com/questions/4814514/http-request-life-cycle)
* [Mapping Relational Database concepts to MongoDB](http://code.tutsplus.com/articles/mapping-relational-databases-and-sql-to-mongodb--net-35650)
* [Getting Started with MongoDB](http://code.tutsplus.com/tutorials/getting-started-with-mongodb-part-1--net-22879)
* [MongoDB Data modeling](http://docs.mongodb.org/manual/core/data-modeling-introduction/)

### Outline

* __Get experience using an [API](http://en.wikipedia.org/wiki/Application_programming_interface) to access the [NYT](http://www.nytimes.com/) articles__
* __Store the retrieved articles and metadata in [MongoDB](http://www.mongodb.org/)__ 
* __Use [regular expressions](http://en.wikipedia.org/wiki/Regular_expression) in [Python](http://docs.python.org/2/howto/regex.html) to search for all articles that contain the word 'Zipf' or 'Zipfian'__
* __Have [FUN!](http://media.giphy.com/media/LlmVkDId8FzP2/giphy.gif) (that's an order)__

![fun](http://24.media.tumblr.com/b7665f65538c9cd195521aa948d67d3d/tumblr_mk2e9tYHAU1s7rl3mo1_500.gif)

## Reference

### ReST
* [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer)
* [Haters gonna HATEOAS](http://timelessrepo.com/haters-gonna-hateoas)
* [Fielding's Dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
* [List of common HTTP status codes](http://www.smartlabsoftware.com/ref/http-status-codes.htm)
* [API client](http://www.getpostman.com/)
* [What is a "protocol"?](http://thomaslevine.com/!/street-sign-protocol)

### Libraries
* [Requests HTTP library](http://docs.python-requests.org/en/latest/)
* [Beautiful Soup (HTML parsing and searching)](http://www.crummy.com/software/BeautifulSoup/)
* [MongoDB Python driver](http://api.mongodb.org/python/current/tutorial.html)
* __[wget](http://www.gnu.org/software/wget/)__ is your friend. 
* __[curl](http://curl.haxx.se/)__ is your homie. 
* __[Variety](https://github.com/variety/variety)__ Schema Analyzer
* __[boilerpipe](https://github.com/misja/python-boilerpipe)__ article extraction
* [Web Scraping Tools](http://blog.marcua.net/post/74655674340)
* [Visual Guide to NoSQL](http://blog.nahurst.com/visual-guide-to-nosql-systems)

### REGEX
* [Regular expression tester](http://pythex.org/)
* [Google Regex tutorial](https://developers.google.com/edu/python/regular-expressions)

### Books

* Programming Collective Intelligence (Python): Chapter 4 (p.54 - p.69)
* Programming Collective Intelligence (Python): Chapter 6
* Machine Learning in Action (Python): Chapter 4.5
* Machine Learning for Hackers (R): Chapter 3

### Tools to store the Data

* Relational Database: Postgres and MySQL 
* NoSQL: Document (MongoDB, CouchDB), Key-Value (Redis), Distributed (HDFS, Riak, Cassandra)
* Text Files (never hurt anyone)

### Data Sources

#### Bulk Downloads -- just like the good ol' days

* FTP servers
* Amazon S3 public [datasets](http://aws.amazon.com/publicdatasets/)
* [InfoChimps](http://www.infochimps.com/datasets)
* Academia -- [Stanford](http://snap.stanford.edu/data/) and [UCI](http://archive.ics.uci.edu/ml/)

#### APIs -- public and hidden
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

#### DIY
* Webscraping 
* manual downloads
