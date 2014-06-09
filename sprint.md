Scrape [this](http://www.snackdata.com) site and use it's data to answer the following questions (using mongoDB queries):

1. Create a record in mongoDB  for each snack containing the following:
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
4. Make a histogram of the dates snacks have been added.
