## Main Event

For this sprint we will scape reviews from Yelp.  Just like the NYT (and any large tech company really), Yelp's power comes from its data in aggregate.  They will happily give you information on one restaurant or the text of one review, but it is quite hard to get __ALL__ of the reviews for __ALL__ of the restaurants.

Assume we are building a restaurant recommendation engine based on user reviews.  The first step in this process is to get data.

1. Using the [Yelp API](http://www.yelp.com/developers/documentation/v2/search_api) find all New American Restaurants in SF.
2. Store these records into MongoDB.
2. How many New American Restaurants are there in SF?
3. For each Restaurant, scrape the first page of reviews from each restaurant.
4. For each Restaurant count how many 5 star reviews it has gotten.
5. Now try to get metadata on all of the restaurants in SF

## (Extra) Fun!

Scrape [this](http://www.snackdata.com) site and use it's data to answer the following questions (using mongoDB queries):

1. Create a record in mongoDB for each snack containing the following:
    * Name
    * Number of snack
    * Flavor
    * Cuisine
    * Series
    * Composition ([reference](http://docs.mongodb.org/manual/tutorial/model-referenced-one-to-many-relationships-between-documents/) these to the appropriate documents in mongo)
    * Date it became an official snack
    * Description (text before taste description)
    * Taste description

1. How many snacks are there in total?
2. What is the most (and least) common cuisine?
3. Which is the most common ingredient across snacks (look at composition)?
4. What are the five most recently added snacks?
5. Make a histogram of the dates snacks have been added.
