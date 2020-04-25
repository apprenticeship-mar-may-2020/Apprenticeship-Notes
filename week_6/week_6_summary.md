
##### This is week 6:
https://github.com/codurance/apprenticeship/wiki/Week-6

# Week 6 - Databases 

Exit Criteria → “Be able to model and implement the persistence of any application”

Objectives: 
  * Modeling the persistence for the application
  * Working on Relational databases
  * Working on Non-Relational document databases

Dependencies: depends on module 2

Previous/secondary goals:

* Types / Modeling (Extreme Learning) : 1 day
  * Types: Relational / Document / Column / Key Value
  * Where different types are stuitable
  * Strengths and weaknesses
  * Design considerations for each type


# Roadmap

[Share folder with material](https://drive.google.com/drive/u/0/folders/1Ic-1MgzKnoGIekz6ItZifhmVJeBBXtEJ)

[Practise](https://docs.google.com/document/d/1x5tw-r2JJg2-MH6rSBP4ae9hGOh4SDxag_ypWCd_4Vo/Redit)

[Repository with example](https://github.com/ferwguerra/example-jpa)

## Day 1: Welcoming, Introduction to DB, Types of DBs, EDR and playground

**Morning**

* Hello World (Presentation of teachers)

* [Introduction to Databases](https://docs.google.com/presentation/d/1_AykLFUobCKPydRDc96ra3tPEwx_vP460mQBjZ-M64s/edit#slide=id.g4190a851a3_0_245)

* Extreme Learning (XL): Types of Databases: Relational, Document, Key value, Wide Column.

**Afternoon**

* [Normalization & Entity Relationship Diagram](https://docs.google.com/presentation/d/1qw-TRm0zrKkC722WqhBUCcgcYgK2-UxkLxVjatxmeO8/edit?usp=sharing)

* [Show example ERD](https://docs.google.com/document/d/1tV_b_xsaAkHW0QklbWSE8slQGOSpWCQh8DgyKljhkXc/edit)

* Practise - Set up environment

* Practise - Exercise Part 1 - Create an EDR


## Day 2: Implementing EDR, JDBC and ORM

**Morning**

* Practise - Exercise Part 2 - Implement EDR in MySql

* [JDBC Presentation](https://docs.google.com/presentation/d/1ag2LIIcW6XoukiQUVoPg76DIwskMfDRwxTwLPlyy88M/edit)

* Practise - Exercise Part 3 - Java project with JDBC


**Afternoon**

* [ORM Presentation](https://docs.google.com/presentation/d/1ag2LIIcW6XoukiQUVoPg76DIwskMfDRwxTwLPlyy88M/edit)

* Practise - Exercise Part 4 - Java project with Spring JPA + Hibernate


## Day 3: Advanced concepts (transactions, indexes, CAP theorem, execution plan), Advanced operations (JOIN, GROUP BY, ORDER BY)

**Morning**

* Extreme Learning (XL): Transactions, indexes, CAP theorem, execution plan

* [Advanced operations](https://docs.google.com/presentation/d/1raQrl6BTCqbqcOQr9GnAj9cvLTP1eigTDD4h0q1to7Q/edit#slide=id.g41bf4bd4c0_0_5)

* Practise - Exercise Part 5 - Queries


**Afternoon**

* [Example Java project working with Spring JPA and JOINs](https://github.com/ferwguerra/example-jpa)

* Practise - Exercise Part 6 - Java project with Spring JPA + Hibernate - Complex operations

## Day 4: Continue with practise, Presentation of Document Dbs

**Morning**

* Continue with practise - Part 6

**Afternoon**

* [Document DBs Introduction](https://docs.google.com/presentation/d/1XytmcCnysyzCrBGC8LOLMpp3DScIX4d2-Hh5RjRJ0P0/edit)

## Day 5: Practise with Document DBs, Recap and Retrospective

**Morning**

* Practise - Exercise Part 7 - Document Dbs

**Afternoon**

* Recap and retrospective


----

## Alternative Roadmap

[The slides from when Rich did the database module](https://docs.google.com/presentation/d/1ajYRsucd28uJPpFya5LUpvPQ8vWRXjwWI2cCwW5pwHM/edit?usp=sharing)

[Chris' slides on NoSQL databases](https://docs.google.com/presentation/d/1_H4t5puQIf4SA629qlOh78Fo7U_mNnB2VKaVP3JdcyI/edit?usp=sharing)

* Relational Databases : 2 days
  * Entity Relationship Diagrams
    * Study the tutorial on ERDs (video: https://www.youtube.com/watch?v=DpZTx5wfXyk)
  * DDL: CREATE TABLE / CREATE INDEX
    * Introduce basic Schema/Table/Column/PrimaryKey/ForeignKey/Index concepts
    * Create DDL from a provided Entity Relationship Diagram and use that as the first migration
  * SQL / Non-SQL / REST
    * Insert Sample DATA using the script provided (must check against the DDL created)
    * Query tables with selects (including multi-table selects - no explicit joins)
  * Joins : Outer, Inner, Left / Right
    * Query tables with explicit joins (requirements to force this kind of statements)
  * Grouping : Group BY,  Aggregate functions (e.g. sum count etc.)
    * Query tables for aggregated results (examples to force group-by)
  * Transactions
    * Theory around transactions (ACID) and 2 Phase-Commit
    * Talk about Eventual Consistency (BASE / CAP Theorm)
    * Coding exercise using Transactions
  * Views
    * Materialised View & Non-material views
  * Database Migrations
    * Introduce migrations as part of the deployment/testing process in CI envs
  * Indexing 
  * Query Optimisation
    * Explain plan exercise with an existing query
    * With & Without indexes, intermediate results sets etc.
  * Data Access Frameworks (ORM, ODBC, JDBC etc.)
    * Barebones DB Connectivity (JDBC, ODBC etc.)
    * JPA / Entity Framework 
    * Pros & Cons
    * Exercise for an integration test for ORM Mappings
* No SQL Databases (Class Based / Katas) : 2 days
  * DDL: CREAT DOCUMENT / CREATE INDEX
  * MAP REDUCE / JOINS
  * Partitioning / Clustering
  * Grouping : Group BY,  Aggregate functions (e.g. sum count etc.)
  * Transactions
  * Database Migrations
  * Data Access Frameworks 


* Working on Relational databases
  * Normalization
  * Explain execution plan
  * Indexes
  * Relationships
* Working on Non-Relational document databases
  * Document/Key-Value (MongoDB/DynamoDB)
  * Indexes
