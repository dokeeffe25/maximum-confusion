---
layout: post
title:  "Elasticsearch Primer"
date:   2017-06-14 18:36:59 +0100
categories: primer tutorial whirlwind
---
I use Elasticsearch quite a lot but there's always been some stuff I'm a little
bit unsure about, like sharding and replication. I think this just comes down to
never taking the time to read about it properly so I've given it a go and cleared
it up in my head.

The best way to cement something in your head is to try and teach someone else, 
so enjoy my whirlwind primer into the core concepts of Elasticsearch.

# What the hell is Elasticsearch?
It's basically a fancy database engine. Not the SQL sort, the NOSQL sort. Data is stored 
in JSON documents rather than the table rows and columns you see in SQL. These JSON
documents are grouped together based on an **index**, which is basically the equivalent
of a SQL database. You can then further break down the documents according to **type**
which again if you're coming from the SQL world you can relate to table.

Elasticsearch doesn't actually market itself as a database, but instead as a search engine.
Under the hood, it uses [Apache Lucene](https://lucene.apache.org/). This is an open
source Java library for searching, spell checking and all the stuff you need to make
that work like tokenisation of words etc. Elasticsearch is also written in Java
although there's clients for different languages and a REST client as well.

Long story short, Elasticsearch is a database engine with some really powerful
search query capabilities baked into it. Obviously this makes it really useful
for analysing large amounts of textual data, like logs (something they market heavily
and have built some complementary tools for).

# Key Concepts
## Cluster
Elasticsearch is distributed and so provides a level of fault tolerance.
You can have multiple instances of Elasticsearch on different (or the same) machines
communicating with each other. These different instances together form a cluster.

## Node
This is pretty straightforward once you understand the idea of a cluster. A node
is just an elasticsearch instance that is a member of a cluster, so in most cases
each node will be on a different machine.

## Shard
Since we have multiple nodes, we can share the load of data between them. An index
(remember, that's basically a database) is broken up and distributed across the
different nodes in a cluster. This is known as sharding and each piece is called
a shard.

## Replication
So far in the story, you've probably noticed a problem. If a machine breaks, any
shards stored on that machine are gone too. Not ideal. To combat this, Elasticsearch
supports replication. Multiple copies of the same shard can be stored on different
nodes. That way, if a machine goes down the data will still be available.
The amount of replication and the amount of shards is all configurable.

Shards can then be split into primary shards and replica shards. Primary shards
are the original copy of the data and replica shards are the copies.

## Document
We've talked about indices and types already, but what the about the data itself.
Like I said earlier, documents take the form of JSON objects in Elasticsearch.

Here's an example document taken from the [Elasticsearch Getting Started Section](https://www.elastic.co/guide/en/elasticsearch/reference/current/_exploring_your_data.html):
    
    {
        "account_number": 0,
        "balance": 16623,
        "firstname": "Bradshaw",
        "lastname": "Mckenzie",
        "age": 29,
        "gender": "F",
        "address": "244 Columbus Place",
        "employer": "Euron",
        "email": "bradshawmckenzie@euron.com",
        "city": "Hobucken",
        "state": "CO"
    }    

This all pretty self-explanatory, it's just a document with some arbitrary data.
You might be wondering though, where's the ID? How can we search for this?

The document we're looking at above is actually just what's known as the **source**
of the document. In reality, a full document looks more like this:

    {
       "_index":    "bank",
       "_type":     "account",
       "_id":       "123",
       "_version":  1,
       "_source": {
                      "account_number": 0,
                      "balance": 16623,
                      "firstname": "Bradshaw",
                      "lastname": "Mckenzie",
                      "age": 29,
                      "gender": "F",
                      "address": "244 Columbus Place",
                      "employer": "Euron",
                      "email": "bradshawmckenzie@euron.com",
                      "city": "Hobucken",
                      "state": "CO"
                  } 
    }
    
This makes more sense. You can see the index, type and ID of the document here.
A document ID can either be generated automatically by Elasticsearch upon indexing
or explicitly stated by you. 

One aside, the *_version* field here just specifies how many times this document
has been updated.

## Terms
Final thing: Elasticsearch queries can be very exact. A common query you'll make is a term
query, which searches for exact matches. For example, if you search for 'foo',
'Foo' and 'FOO' will not match despite their similarity. If you make use of term
queries in your data, be sure that your data conforms to these semantics. Other
queries are more relaxed in their matching, so queries that match on all three of
the terms above are possible.



That's it for my Elasticsearch Primer. I haven't really talked about queries here,
purely because it's a pretty huge topic. Hopefully this will give you an understanding
of the basic structure of an Elasticsearch cluster though.
 
Let me know if there's any questions, or if I've made a mistake anywhere. 
I think I might try more of these whirlwind tours of different tools in the 
future and see if I can learn more in the process.


