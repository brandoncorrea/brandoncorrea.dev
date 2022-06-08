---
layout: post
title:  "Datomic (Free)!"
date:   2021-11-29 7:52:05 -0400
categories: clojure, apprenticeship
---

After hours of Googling and YouTube, I finally have Datomic working in my application!
I'll go over how to set up the free version locally and some areas where I got stuck.

### Starting your Datomic Server

#### Download

The first thing that you'll need to do is to download 
[Datomic Free][datomic-download]. You can go ahead and install the latest version 
(currently 0.9.5703.21) and be sure not to lose the download path–you'll need that 
for this next part.

Once Datomic is downloaded and unzipped, you should see a couple files:
- `bin/transactor`
- `config/samples/free-transactor-template.properties`

#### Execute

Open the directory in your console and run the command:
- Mac: `bin/transactor config/samples/free-transactor-template.properties`
- Windows: `.\bin\transactor config\samples\free-transactor-template.properties`

After executing the command, you should see something like this in the console:
````
Starting datomic:free://localhost:4334/<DB-NAME>, storing data in: data ...
System started datomic:free://localhost:4334/<DB-NAME>, storing data in: data
````

Your connection URI (to be used later) is that 
`datomic:free://localhost:4334/<DB-NAME>` string. `<DB-NAME>` should be replaced with
whatever you want to name your database.

Once you have the Datomic server up and running, you should be able to move this out of
your install directory.

### Adding Datomic to your Project

#### Creating a Schema

Before we start querying our Datomic server, we will need to define our database 
structure with a schema, which can be saved anywhere in the project's folder. 

Suppose we just want to be able to save people's names and ages. 
All we need in our schema is this:

````clojure
[{:db/ident       :person/first
  :db/valueType   :db.type/string
  :db/cardinality :db.cardinality/one}
 {:db/ident       :person/last
  :db/valueType   :db.type/string
  :db/cardinality :db.cardinality/one}
 {:db/ident       :person/age
  :db/valueType   :db.type/long
  :db/cardinality :db.cardinality/one}]
````

Here's a short breakdown of the schema definition:
- Each map is a column
- `:db/ident` is the name of the column
  - `person` is the table name
  - `first`, `last`, and `age` are the column names
- `:db/valueType` is the _type_ of data the column holds
  - strings, integers, etc.
- `:db/cardinality` represents the relationship of the column to the record being added
  - one-to-one, one-to-many, many-to-one, many-to-many

For simplicity, everything is one-to-one in this example. You can read up on 
[datomic schemas][datomic-schema] if you need more complex data structures.

#### Updating Dependencies

Now that we have the schema, we need to add a dependency to `datomic-free`.

In `project.clj`, add the [latest version][datomic-clojars] of `datomic-free` to your
list of `:dependencies`. This will allow you to reference `datomic.api` in your source files.

````clojure
(defproject 
  ;...
  :dependencies [;...
                 [com.datomic/datomic-free "0.9.5697"]]
  ;...
  )
````

#### Connecting to Datomic

Next, we will need to create a connection to Datomic in our source file.
Be sure to require `datomic.api`.

First, we'll define our connection URI. You can use either an in-memory 
connection or your datomic server's URI from the first step.

````clojure
; Datomic Server
(def uri "datomic:free://localhost:4334/people")
; In-Memory
(def uri "datomic:mem://people")
````

You'll also need to have your database schema handy, so be sure to define that as well.

````clojure
(def schema (load-file "person_schema.edn"))
````

Now we need to make sure the database exists using `create-database`. If it already exists, 
then nothing will happen–your database and its data will remain unaffected.

After that, you can `connect` to the database, load your `schema`, 
and return the connection to the caller. 

````clojure
(defn connect []
  (create-database uri)
  (let [conn (connect uri)]
    (transact conn schema)
    conn))
````

This connection will be used for each database call.

#### Saving Data

To add a record, just place the record into a vector and pass it into 
the `transact` function along with the database connection.

````clojure
(defn save-person [conn first last age]
  @(transact conn
             [{:person/first first
               :person/last  last
               :person/age   age}]))
````

You can add as many records as you'd like in the `transact` vector, 
and they will all be added to the database.

I haven't had any need for this, but if you record the value returned by this function,
you'll receive back something like this:

````clojure
=> (save-person (connect) "Bill" "Murray" 71)
{:db-before datomic.db.Db@317d6b7 
 :db-after  datomic.db.Db@551da7ce
 :tx-data   [#datom[13194139534313 50 #inst "2021-11-30T04:28:34.549-00:00" 13194139534313 true] 
             #datom[17592186045418 63 "Bill" 13194139534313 true] 
             #datom[17592186045418 64 "Murray" 13194139534313 true] 
             #datom[17592186045418 65 71 13194139534313 true]]
 :tempids   {-9223301668109597993 17592186045418}}
````

#### Retrieving Data

Now that we can save data, how do we get this data back for later use?
Well, will just use the `q` function to _query_ the `db` (database) pointed to by the connection.

````clojure
(defn find-all-people [conn]
  (q '[:find ?person-first ?person-last ?person-age
       :where [?eid :person/first ?person-first]
              [?eid :person/last  ?person-last]
              [?eid :person/age   ?person-age]]
      (db conn)))
````

If you're familiar with SQL, this is a lot like writing `SELECT First, Last, Age from People`.
While this datalog query may look a little weird at first, once you become familiar with its
structure it will start to make more sense.

In the `:where` clause, we can name each record property whatever we'd like. 
Here I chose `?eid`, `?person-first`, `?person-last`, and `?person-age`.

The first, last, and age properties may look familiar. We just map these names over to 
the `:person/column-name` keywords. But where does `?eid` come from, and what is it?

When an entity is added to the database, it's automatically given an 
ID that can be used for identifying each of a record's parts. So here we
are joining each part of the records by this ID.

In `:find`, we _select_ which items we want to return and in what order they should 
appear in the result. If we ran this function with different `:find` orders, 
here's what the results would be.

````clojure
; :find ?person-first ?person-last ?person-age
=> (find-all-people (connect))
#{["Bill" "Murray" 71] ["Nick" "Offerman" 51] ["Harrison" "Ford" 79]}

; :find ?person-age ?eid ?person-first
=> (find-all-people (connect))
#{[71 17592186045418 "Bill"] [79 17592186045420 "Harrison"] [51 17592186045422 "Nick"]} 
````

#### Closing Connections

There was one issue I ran into where my application would hang at the command prompt
when it should have terminated. This has to do with database connections remaining open
within the application.

While you could always do a `(System/exit 0)` to close your application, 
there _is_ a proper way to close your database connections.

Using Datomic's `(shutdown true)` function, your connections will allow your 
application to close gracefully once it has reached a termination point.

While this works from an integration perspective, I noticed while testing that 
I was still able to perform queries using the connection initially created.
To close the connection, you can use Datomic's `release` function. However,
this must be called _before_ the `shutdown` function, otherwise you will 
receive a nice little exception message about Clojure's displeasure.

I ended up creating this function to handle open connections:

````clojure
(defn disconnect [conn]
  (release conn)
  (shutdown true))
````

Now that you've got Datomic all set up, start building some 
crazy data structures and queries!

[datomic-download]: https://my.datomic.com/downloads/free
[datomic-clojars]: https://clojars.org/com.datomic/datomic-free
[datomic-schema]: https://docs.datomic.com/on-prem/schema/schema.html