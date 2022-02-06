# Fluree
## What is Fluree?
Fluree is an immutable graph database that, beyond performing typical modern database functions, emphasizes security, trust, provenance, privacy, and interoperability. Fluree is open source, under the AGPL license, and built on open standards. Fluree can be run centralized, distributed, or decentralized.

This tutorial was executed on Ubuntu via Windows Subsystem for Linux (WSL), following the steps of Fluree Documentation (https://developers.flur.ee/docs/overview/getting_started/)

Fluree publishes a Docker image to Docker Hub.
Run these with:
```shell
docker run -d --restart=always -p 8090:8090 fluree/ledger:latest
```
...and then access the admin dashboard at http://localhost:8090.

On the admin dashboard, create new ledger by clicking 'Add Ledger' and providing a unique name of a network and ledger.

## Overview
In Fluree, a schema is comprised of collections and predicates. While Fluree is a graph database, if you come from the relational world you can think of a collection sort of like a database table. Every time you want a new type of item in your ledger, you would create a new collection. For example, collections in your ledger might be person, company, or city.

## Adding Collections
The below transaction adds five new collections: person, chat, comment, artist, and movie. You can issue this transaction in your user interface.

Fluree supports querying through:
* FlureeQL
* Curl
* GraphQL
* SPARQL

To create a new schema, first create a collection:
```shell
[{
    "_id": "_collection",
    "name": "movies",
    "doc": "List of Best Movies"
}]
```
Click on the arrow in the top right corner to send the transaction.

To add predicates to the created collection choose Transact once again and type:

```shell
[
    {
        "_id": "_predicate",
        "name": "movies/name",
        "type": "string"
    },
    {
        "_id": "_predicate",
        "name": "movies/genre",
        "type": "string"
    },
    {
        "_id": "_predicate",
        "name": "movies/year",
        "type": "int"
    },
    {
        "_id": "_predicate",
        "name": "movies/rating",
        "type": "integer"
    }
]
```

Click on the arrow in the top right corner to send the transaction.

In the Schema view, the transactions effects should be visible.
Adding data To add data for a created car schema, open Transact window and type:
```shell
[
    {
        "_id": "movie",
        "name": "The Lord of the Rings: The Fellowship of the Ring",
        "genre": "Adventure",
        "year": 2001,
        "rating": 5
    },
    {
        "_id": "movie",
        "name": "The Lord of the Rings: The Two Towers",
        "genre": "Adventure",
        "year": 2002,
        "rating": 4
    },
    {
        "_id": "movie",
        "name": "The Lord of the Rings: The Return of the King",
        "genre": "Adventure",
        "year": 2003,
        "rating": 4
    }
]
```
As always click on the arrow in the top right corner to send the transaction.
Querying the data Click on Query and choose FlureeQL option. Then type:
```shell
{
    "select": ["*"],
    "from": "movies"
}
```
And click on the arrow in the top right corner. It will display on the cars added in the previous step.

To display i.e. only model and year for all the cars, type:
```shell
{
  "select": ["movies/name", "movies/year"],
  "from": "movies"
}
```