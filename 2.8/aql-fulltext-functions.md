---
layout: default
description: Filter data with AQL fulltext indexes
---
Fulltext functions
==================

AQL offers the following functions to filter data based on [fulltext indexes](glossary.html#fulltext-index):

- `FULLTEXT(collection, attribute, query, limit)`: 
  Returns all documents from collection *collection* for which the attribute *attribute*
  matches the fulltext query *query*. The *limit* parameter is optional. If set to a non-zero
  value, it will cap the result to at most this number of documents.
  *query* is a comma-separated list of sought words (or prefixes of sought words). To 
  distinguish between prefix searches and complete-match searches, each word can optionally be
  prefixed with either the `prefix:` or `complete:` qualifier. Different qualifiers can
  be mixed in the same query. Not specifying a qualifier for a search word will implicitly
  execute a complete-match search for the given word:

  - `FULLTEXT(emails, "body", "banana")`<br>
    Will look for the word *banana* in the 
    attribute *body* of the collection *collection*.

  - `FULLTEXT(emails, "body", "banana,orange")`<br>
    Will look for both words
    *banana* and *orange* in the mentioned attribute. Only those documents will be
    returned that contain both words.

  - `FULLTEXT(emails, "body", "prefix:head")`<br>
    Will look for documents that contain any
    words starting with the prefix *head*.

  - `FULLTEXT(emails, "body", "prefix:head,complete:aspirin")`<br>
    Will look for all 
    documents that contain a word starting with the prefix *head* and that also contain 
    the (complete) word *aspirin*. Note: specifying `complete:` is optional here.

  - `FULLTEXT(emails, "body", "prefix:cent,prefix:subst")`<br>
    Will look for all documents 
    that contain a word starting with the prefix *cent* and that also contain a word
    starting with the prefix *subst*.

  If multiple search words (or prefixes) are given, then by default the results will be 
  AND-combined, meaning only the logical intersection of all searches will be returned. 
  It is also possible to combine partial results with a logical OR, and with a logical NOT:

  - `FULLTEXT(emails, "body", "+this,+text,+document")`<br>
    Will return all documents that 
    contain all the mentioned words. Note: specifying the `+` symbols is optional here.

  - `FULLTEXT(emails, "body", "banana,|apple")`<br>
    Will return all documents that contain
    either (or both) words *banana* or *apple*.

  - `FULLTEXT(emails, "body", "banana,-apple")`<br>
    Will return all documents that contain
    the word *banana* but do not contain the word *apple*.

  - `FULLTEXT(emails, "body", "banana,pear,-cranberry")`<br>
    Will return all documents that 
    contain both the words *banana* and *pear* but do not contain the word 
    *cranberry*.

  No precedence of logical operators will be honored in a fulltext query. The query will simply
  be evaluated from left to right.
  
**Note**: the `FULLTEXT()` function requires the collection *collection* to have a
fulltext index on *attribute*. If no fulltext index is available, this function
will fail with an error. `FULLTEXT()` is not meant to be used as an argument to `FILTER`
but rather to be used as the expression of the `FOR` statement:

```js
FOR oneMail IN
  FULLTEXT(emails, "body", "banana,-apple")
  RETURN oneMail._id
```
