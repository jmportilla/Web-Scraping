

```python
# before using pymongo, you must open a new terminal window and type in 
# $ mongod
import pprint as pp
```


```python
# ! pip install pymongo
from pymongo import MongoClient

# CONNECT TO THE TERMINAL WINDOW THING
client = MongoClient('mongodb://localhost:27017/')

# CREATE AND/OR USE THIS DATABASE
db = client['tutorial_database']

# INSEPCT THE DATA BASE
db.collection_name.find_one() == db['collection_name'].find_one()
# True dat!
```




    True




```python
some_data = {"Rapper": "Lil Wayne",
             "Birth-Name": "Dwayne Michael Carter",
             "Best Rapper Alive": True,
             "Occupations": ['Rapper', 'songwriter', 'producer','money getter', 'player'] 
             } 
db.collection_name.insert(some_data)
```




    ObjectId('5432d48a21dc020481ee96aa')




```python
some_data = {"ID": "Lil Wayne",
             "Label": "Cash Money",
             "Occupation": "Rapper",
             "Albums": ["Tha Block Is Hot", 'Lights Out', '500 Degreez', 'Tha Carter', 'Tha Carter II', 'Like Father Like Son', 'Tha Carter III', 'Rebirth', 'I Am Not A Human Being', 'Tha Carter IV', 'I Am Not A Human Being II', 'Tha Cater V'] }

db.collection_name.insert(some_data)
```




    ObjectId('5432d48a21dc020481ee96ab')




```python
db.collection_name.remove({"Rapper": "Lil Wayne"})
```




    {u'connectionId': 4, u'err': None, u'n': 1, u'ok': 1.0}




```python
db.collection_names()
```




    [u'system.indexes', u'collection_name']




```python
db.collection_name.find_one( {"ID": "Lil Wayne"} )

# OR...
for x in db.collection_name.find( {"ID": "Lil Wayne"} ):
    print x
```

    {u'Albums': [u'Tha Block Is Hot', u'Lights Out', u'500 Degreez', u'Tha Carter', u'Tha Carter II', u'Like Father Like Son', u'Tha Carter III', u'Rebirth', u'I Am Not A Human Being', u'Tha Carter IV', u'I Am Not A Human Being II', u'Tha Cater V'], u'_id': ObjectId('5432d48a21dc020481ee96ab'), u'Label': u'Cash Money', u'ID': u'Lil Wayne', u'Occupation': u'Rapper'}



```python
more_data = {"ID": "Kanye West",
             "Occupation": "Rapper",
             "Label": "Roc-A-Fella",
             "Albums": ['The College Dropout', 'Late Registration', 'Graduation',  '808s & Heartbreak', 'My Beautiful Dark Twisted Fantasy', 'Watch the Throne', 'Yeezus'] }

db.collection_name.insert(more_data)
```




    ObjectId('5432d48f21dc020481ee96ac')




```python
# db.collection_name.remove({"Rapper": "Lil Wayne"})
# db.collection_name.remove({"ID": "Lil Wayne"})
# db.collection_name.remove({"ID": "Kanye West"})
```


```python

```


```python
for i in db.collection_name.find():
    print pp.pprint(i)
    print "-----------"
```

    {u'Albums': [u'Tha Block Is Hot',
                 u'Lights Out',
                 u'500 Degreez',
                 u'Tha Carter',
                 u'Tha Carter II',
                 u'Like Father Like Son',
                 u'Tha Carter III',
                 u'Rebirth',
                 u'I Am Not A Human Being',
                 u'Tha Carter IV',
                 u'I Am Not A Human Being II',
                 u'Tha Cater V'],
     u'ID': u'Lil Wayne',
     u'Label': u'Cash Money',
     u'Occupation': u'Rapper',
     u'_id': ObjectId('5432d48a21dc020481ee96ab')}
    None
    -----------
    {u'Albums': [u'The College Dropout',
                 u'Late Registration',
                 u'Graduation',
                 u'808s & Heartbreak',
                 u'My Beautiful Dark Twisted Fantasy',
                 u'Watch the Throne',
                 u'Yeezus'],
     u'ID': u'Kanye West',
     u'Label': u'Roc-A-Fella',
     u'Occupation': u'Rapper',
     u'_id': ObjectId('5432d48f21dc020481ee96ac')}
    None
    -----------



```python
# COUNT THE NUMBER OF DOCUMENTS IN YOUR TABLE
db.collection_name.count()
```




    2




```python
# COUNT THE NUMBER OF ITEMS THAT MATCH YOUR QUERY
db.collection_name.find({"Occupation": "Rapper"}).count()
```




    2


