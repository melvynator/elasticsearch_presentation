# An introduction to elasticsearch


Let's create an index
```json
PUT idealab
{
    "settings" : {
        "index" : {
            "number_of_shards" : 1, 
            "number_of_replicas" : 0
        }
    }
}
```

Definition of a mapping
```json
PUT idealab/_mapping/people 
{
  "properties": {
    "name": { "type": "text"},
    "age": { "type": "integer"},
    "max_degree": {"type": "keyword"},
    "nationality":{"type": "keyword"},
    "is_graduated":{"type": "boolean"},
    "is_student":{"type": "boolean"},
    "description": {"type": "text"}
  }
}
```
Update a mapping
```json
PUT idealab/_mapping/people 
{
  "properties": {
    "thesis_topic": {
      "type": "text"
    }
  }
}
```

POST VS PUT
Add someone using PUT
```json
PUT idealab/people/0
{
  "name": "Eric",
  "age": 23,
  "max_degree": "bachelor",
  "nationality": "Taiwanese",
  "is_graduated": false,
  "is_student": true,
  "description": "Eric is the man of the lab, always there to buy stuff for the lab or fix the server and the more important he is the manager of the AC without him we will be all dead so far. So sad he is always feeding",
  "thesis_topic" : "Eric work on the mental trouble. More specificly on the prediction of bipolar behavior."
}
```

Add someone using POST
```json
POST idealab/people
{
  "name": "Eric",
  "age": 23,
  "max_degree": "bachelor",
  "nationality": "Taiwanese",
  "is_graduated": false,
  "is_student": true,
  "description": "Eric is the man of the lab, always there to buy stuff for the lab or fix the server and the more important he is the manager of the AC without him we will be all dead so far. So sad he is always feeding",
  "thesis_topic" : "Eric work on the mental trouble. More specificly on the prediction of bipolar behavior."
}
```

Search someone by ID
```json
GET idealab/people/AVz319Y2vn0a0PzUHe_
```

Delete the index
```json
DELETE idealab/people/AVz319Y2vn0a0YPzUHe_
```

GET a specific document (from the index idealab the document type people and the id 0)
```json
GET idealab/people/0
```

Add more data
```json
PUT idealab/people/1
{
  "name": "Jherez",
  "age": 22,
  "max_degree": "bachelor",
  "nationality": "Kittitians",
  "is_graduated": false,
  "is_student": true,
  "description": "Jherez is 0% fat, gym coach, you can often find him at the gym 'working out' or in the lab doing some experiments. He has probably some spanish blood since he doesn't seem to have a regular time to eat, it's not unusual to see him going to grab a diner/lunch/breakfast/souper at 10pm. He also likes to crack open a cold one with the friends",
  "thesis_topic" : "Working on hate speeches he became quickly a reader of some dark websites and specialize himself into the detection of code words in hate speeches"
}

PUT idealab/people/2
{
  "name": "Jiajia",
  "age": 23,
  "max_degree": "bachelor",
  "nationality": "Taiwanese",
  "is_graduated": false,
  "is_student": true,
  "description": "Jiajia main concern seem to be to feed the ennemy team. She is always feeding. Except feeding she wants to go to France to feed on the EU server as well",
  "thesis_topic" : "Jiajia is working on the interest of users in different topics using emotion analysis"
}

PUT idealab/people/3
{
  "name": "Fabio",
  "age": 24,
  "max_degree": "bachelor",
  "nationality": "Sao Tomean",
  "is_graduated": false,
  "is_student": true,
  "description": "Fabio go to the gym only to be sure that Melvyn doesn't kill himself while lifting weight, because he definitly don't need to. He refuses all KFC food by loyalty to MC Donald. Recently his country break relationship with Taiwan.",
  "thesis_topic" : "Working on user interests, he is using a different methodology that Jiajia to detect the interest through time analysis"
}

PUT idealab/people/4
{
  "name": "pen",
  "age": 27,
  "max_degree": "master",
  "nationality": "Chinese",
  "is_graduated": true,
  "is_student": false,
  "description": "Recently leaves Taiwan to go back to china"
}

PUT idealab/people/5
{
  "name": "Kai",
  "age": 22,
  "max_degree": "bachelor",
  "nationality": "Taiwanese",
  "is_graduated": false,
  "is_student": true,
  "description": "Kai dance while working and listen to bad musics. Fine singer, he enjoys KTV, try to learn French, but his girlfriend doesn't like the courses that Kai took"
}

PUT idealab/people/6
{
  "name": "Professor Chen",
  "age": 30,
  "max_degree": "PhD",
  "nationality": "Taiwanese",
  "is_graduated": true,
  "is_student": false,
  "description": "Manage Idea lab and enjoy particulary distributing penalties (especially to Melvyn)"
}
```

A simple search
```json
GET idealab/people/_search
{
  "query": {
    "match": {
      "_all" :"jiajia"
    }  
  }
}
```

Analyser
```json
GET idealab/people/_search
{
  "query": {
    "match": {
      "description": "feed"
    }
  }
}
```

Update/create the index idealab
```json
PUT idealab
{
  "settings": {
    "index" : {
      "number_of_shards" : 1, 
      "number_of_replicas" : 0
    }
  },
  "mappings": {
    "people" : {
      "properties": {
        "name": { "type": "text"},
        "age": { "type": "integer"},
        "max_degree": {"type": "keyword"},
        "nationality":{"type": "keyword"},
        "is_graduated":{"type": "boolean"},
        "is_student":{"type": "boolean"},
        "description": {
          "type": "text",
          "fields": {
            "analysed": {
              "type" : "text",
              "analyzer": "english"
            }
          }
        },
        "thesis_topic": {
          "type": "text",
          "analyzer": "english"
        }
      }
    }
  }
}
```

Delete the index
```json
DELETE idealab
```

Compare the two different analyser
```json
GET idealab/_analyze 
{
  "field": "description.analysed",
  "text": "The quick Brown Foxes."
}

GET idealab/_analyze 
{
  "field": "description",
  "text": "The quick, Brown Foxes."
}
```

Search a document  # see the difference between description and description.analysed
```json
GET idealab/people/_search
{
  "query": {
    "match": {
      "description.analyzed": "feeding"
    }
  }
}
```

What is relevance?
```json
GET idealab/people/_search
{
  "_source": "description", 
  "query": {
    "match": {
      "description.analysed": {
        "query": "lab manager", 
        "operator": "or"
      }
    }
  }
}
```

OR query
```json
GET idealab/people/_search
{
  "query": {
    "match": {
      "description" :{
        "query": "gym lab",
        "operator": "or"
      }  
    }
  }
}
```


Fuzzy query
Why it doesn't work?
Try with 3
Auto recommended
```json
GET idealab/people/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "Erok",
        "fuzziness": 3
      }
    }
  }
}
```

Wildcard
```json
GET idealab/people/_search
{
  "size": 1, 
  "query": {
    "wildcard": {
      "name": {
        "value": "*j*"
      }
    }
  }
}
```

Boolean query
```json
GET idealab/people/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {
          "is_student": {
            "value": false
          }
        }},
        {
          "term": {
            "nationality": {
              "value": "Taiwanese"
            }
          }
        }
      ]
    }
  }
}
```

More like this
```json
GET idealab/_search
{
    "query": {
        "more_like_this" : {
            "fields" : ["description"],
            "like" : [
            {
                "_index" : "idealab",
                "_type" : "people",
                "_id" : "0"
            }
            ],
            "min_term_freq" : 3,
            "max_query_terms" : 10
        }
    }
}

DELETE idealab
```
