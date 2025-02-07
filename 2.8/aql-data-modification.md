---
layout: default
description: AQL supports the following data-modification operations
---
Data modification queries
=========================

AQL supports the following data-modification operations:

- **INSERT**: insert new documents into a collection
- **UPDATE**: partially update existing documents in a collection
- **REPLACE**: completely replace existing documents in a collection
- **REMOVE**: remove existing documents from a collection
- **UPSERT**: conditionally insert or update documents in a collection

### Modifying a single document

Let's start with the basics: `INSERT`, `UPDATE` and `REMOVE` operations on single documents.
Here is an example that insert a document in an existing collection *users*:

	INSERT {firstName:'Anna', name:'Pavlova', profession:'artist'} IN users

You may provide a key for the new document; if not provided, ArangoDB will create one for you.  

	INSERT {_key:'GilbertoGil', firstName:'Gilberto', name:'Gil', city:'Fortalezza'} IN users

As Arango is schema-free, attributes of the documents may vary: 

	INSERT {_key:'PhilCarpenter',  firstName:'Phil', name:'Carpenter', middleName:'G.',status:'inactive', } IN users

	INSERT {_key:'NatachaDeclerck', firstName:'Natacha', name:'Declerck', location:'Antwerp'} IN users 

Update is quite simple. The following AQL statement will add or change the attributes status and location

	UPDATE 'PhilCarpenter' WITH { status:'active', location:'Beijing' }  IN users

Replace is an alternative to update where all attributes of the document are replaced.

	REPLACE   { _key: 'NatachaDeclerck', firstName:'Natacha', name:'Leclerc', status: 'active', level:'premium' } IN users

Removing a document if you know its key is simple as well : 

	REMOVE 'GilbertoGil' IN users

or 

	REMOVE {_key:'GilbertoGil'} IN users


### Modifying multiple documents

Data-modification operations are normally combined with `FOR` loops to
iterate over a given list of documents. They can optionally be combined with
`FILTER` statements and the like.

Let's start with an example that modifies existing documents in a collection
*users* that match some condition:

    FOR u IN users
      FILTER u.status == 'not active'
      UPDATE u WITH { status: 'inactive' } IN users



Now, let's copy the contents of the collection *users* into the collection
*backup*:

    FOR u IN users
      INSERT u IN backup

As a final example, let's find some documents in collection *users* and
remove them from collection *backup*. The link between the documents in both
collections is established via the documents' keys:

    FOR u IN users
      FILTER u.status == 'deleted'
      REMOVE u IN backup


### Returning documents

Data-modification queries can optionally return documents. In order to reference
the inserted, removed or modified documents in a `RETURN` statement, data-modification 
statements introduce the `OLD` and/or `NEW` pseudo-values:

    FOR i IN 1..100
      INSERT { value: i } IN test 
      RETURN NEW

    FOR u IN users
      FILTER u.status == 'deleted'
      REMOVE u IN users 
      RETURN OLD

    FOR u IN users
      FILTER u.status == 'not active'
      UPDATE u WITH { status: 'inactive' } IN users 
      RETURN NEW

`NEW` refers to the inserted or modified document revision, and `OLD` refers
to the document revision before update or removal. `INSERT` statements can 
only refer to the `NEW` pseudo-value, and `REMOVE` operations only to `OLD`. 
`UPDATE`, `REPLACE` and `UPSERT` can refer to either.

In all cases the full documents will be returned with all their attributes,
including the potentially auto-generated attributes such as `_id`, `_key`, or `_rev`
and the attributes not specified in the update expression of a partial update.

#### Projections

It is possible to return a projection of the documents in `OLD` or `NEW` instead of 
returning the entire documents. This can be used to reduce the amount of data returned 
by queries.

For example, the following query will return only the keys of the inserted documents:

    FOR i IN 1..100
      INSERT { value: i } IN test 
      RETURN NEW._key


#### Using OLD and NEW in the same query

For `UPDATE`, `REPLACE` and `UPSERT` statements, both `OLD` and `NEW` can be used
to return the previous revision of a document together with the updated revision:

    FOR u IN users
      FILTER u.status == 'not active'
      UPDATE u WITH { status: 'inactive' } IN users 
      RETURN { old: OLD, new: NEW }


#### Calculations with OLD or NEW

It is also possible to run additional calculations with `LET` statements between the
data-modification part and the final `RETURN` of an AQL query. For example, the following
query performs an upsert operation and returns whether an existing document was
updated, or a new document was inserted. It does so by checking the `OLD` variable
after the `UPSERT` and using a `LET` statement to store a temporary string for
the operation type:
  
    UPSERT { name: 'test' } 
      INSERT { name: 'test' } 
      UPDATE { } IN users
    LET opType = IS_NULL(old) ? 'insert' : 'update'
    RETURN { _key: NEW._key, type: opType }


### Restrictions

The name of the modified collection (*users* and *backup* in the above cases) 
must be known to the AQL executor at query-compile time and cannot change at 
runtime. Using a bind parameter to specify the [collection name](glossary.html#collection-name) is allowed.

Data-modification queries are restricted to modifying data in a single 
collection per query. That means a data-modification query cannot modify 
data in multiple collections with a single query. It is still possible (and
was shown above) to read from one or many collections and modify data in another
within the same query.

Only a single data-modification operation can be used per AQL query. Data-modification
queries cannot be used inside subqueries. Data-modification operations can optionally
be followed by `LET` operations and a single `RETURN` operation to return data. If
expressions are used within these operations, they cannot contain subqueries or 
access data in collections using AQL functions.

Finally, data-modification operations can optionally be followed by `LET` and `RETURN`,
but not by other statements such as `SORT`, `COLLECT` etc.


### Transactional Execution
  
On a single server, data-modification operations are executed transactionally.
If a data-modification operation fails, any changes made by it will be rolled 
back automatically as if they never happened.

In a cluster, AQL data-modification queries are currently not executed transactionally.
Additionally, *update*, *replace*, *upsert* and *remove* AQL queries currently 
require the *_key* attribute to be specified for all documents that should be 
modified or removed, even if a shared key attribute other than *_key* was chosen 
for the collection. This restriction may be overcome in a future release of ArangoDB.
