---
layout: default
description: Welcome to the ArangoDB documentation!
page-toc:
  disable: true
---
ArangoDB {{ site.data.versions[page.version.name] }} Documentation
=====================================

Welcome to the ArangoDB documentation!

The documentation introduces ArangoDB for you as a user, developer and administrator and describes all of its functions in detail. 

ArangoDB is a multi-model, open-source database with flexible data models for documents, graphs, and key-values. Build high performance applications using a convenient SQL-like query language or JavaScript extensions. Use ACID transactions if you require them. Scale horizontally and vertically with a few mouse clicks.

Key features include:

* **Schema-free schemata** let you combine the space efficiency of MySQL with the performance power of NoSQL
* Use ArangoDB as an **application server** and fuse your application and database together for maximal throughput
* JavaScript for all: **no language zoo**, you can use one language from your browser to your back-end
* ArangoDB is **multi-threaded** - exploit the power of all your cores
* **Flexible data modeling**: model your data as combination of key-value pairs, documents or graphs - perfect for social relations
* Free **index choice**: use the correct index for your problem, be it a skip list or a fulltext search
* Configurable **durability**: let the application decide if it needs more durability or more performance
* No-nonsense storage: ArangoDB uses all of the power of **modern storage hardware**, like SSD and large caches
* **Powerful query language** (AQL) to retrieve and modify data 
* **Transactions**: run queries on multiple documents or collections with optional transactional consistency and isolation
* **Replication** and **Sharding**: set up the database in a master-slave configuration or spread bigger datasets across multiple servers
* It is **open source** (Apache License 2.0)

In this documentation you can inform yourself about all the functions, features and programs ArangoDB provides for you.
Features are ilustrated with interactive usage examples; you can cut'n'paste them into [arangosh](arangosh.html) to try them out.
The http REST-API is demonstrated with cut'n'paste recepies intended to be used with the [cURL](http://curl.haxx.se){:target="_blank"}.
Drivers may provide their own examples based on these .js based examples to improve understandeability for their respective users.
I.e. for the [java driver](https://github.com/arangodb/arangodb-java-driver#learn-more){:target="_blank"} some of the samples are re-implemented.

You can also go to our [cookbook](https://docs.arangodb.com/cookbook){:target="_blank"} and look through some recipes to learn more about ArangoDB specific problems and solutions.

### Community

If you have questions regarding ArangoDB, Foxx, drivers, or this documentation don't hesitate to contact us on:

- [Github](https://github.com/arangodb/arangodb/issues){:target="_blank"} for issues and misbehavior or [pull requests](https://www.arangodb.com/community/){:target="_blank"}
- [Google groups](https://groups.google.com/forum/?hl=de#!forum/arangodb){:target="_blank"} for discussions about ArangoDB in general or to announce your new Foxx App
- [Stackoverflow](http://stackoverflow.com/questions/tagged/arangodb){:target="_blank"} for questions about AQL, usage scenarios etc.

Please describe:

- the environment you run ArangoDB in
- the ArangoDB version you use
- whether you're using Foxx
- the client you're using
- which parts of the Documentation you're working with (link)
- what you expect to happen
- whats actually happening

We will respond as soon as possible.


