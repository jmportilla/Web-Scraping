1. `mongo`
1. `db`
2. `show dbs`
3. `use clicks`
4. `show dbs` and `db`
5. `quit()`
6. `curl -s http://developer.usa.gov/1usagov`
7. `curl -s http://developer.usa.gov/1usagov --no-buffer | grep nasa`
8. `mongo`
9. `show dbs`
10. `db.bitly_clicks.help()`
11. `db.bitly_clicks.stats()`
11. `db.bitly_clicks.find()`
12. `db.bitly_clicks.find().limit(25)`
12. `db.bitly_clicks.find({'cy': "San Francisco"})
12. ```js
db.bitly_clicks.find().forEach(
    function(doc) { 
        db.bitly_clicks.update({_id: doc._id}, 
                       {
                        $set : 
                            { 
                                "timestamp": new Date(doc.t * 1000)
                            }
                        }
                    );
        }
    )
```
14. `db.bitly_clicks.find({}).sort({"timestamp": -1}).limit(1)`
13. `var start = ISODate("2014-06-09T09:33:30Z")`
14. `var end = ISODate("2014-06-09T09:34:30Z")`
15. ```js
db.bitly_clicks.find(
    {
        "timestamp": {
            "$gte": start, "$lt": end
        }
    }).count()
```
16. ```js
db.bitly_clicks.aggregate( [
    {      
        $group: {         
            _id: "$u",         
            count: { $sum: 1 }
                   }
    }, 
    {
        $sort: {count: 1}
    } 
] )
```
