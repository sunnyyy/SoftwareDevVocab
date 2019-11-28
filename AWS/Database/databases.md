# AWS databases

## relational vs. non-relational DBs
- relational DBs
  * like traditional spreadsheets
  * are tables with rows and pre-defined fields (columns) --> e.g. first name, last name, gender
  * examples:
    - SQL server
    - Oracle
    - MySQL server
    - PostgreSQL
    - Amazon Aurora (~ MySQL ?)
    - MariaDB
- non-relational DB
  * do ___NOT___ need pre-defined table structure
  * special vocab:
    - collection = table
    - document = row
    - key-value pairs = fields
  * examples:
    - JSON formatted
    - NoSQL

## Data warehousing
- used for business intelligence
- used to pull in large/complex datasets
- usually used by management to do queries on data (e.g. current performance vs. targets, etc.)
  * --> do __*NOT*__ make these queries on your prod DBs! it will cause significant latency
- examples:
  * Cognos
  * Jaspersoft
  * SQL server reporting services
  * Oracle Hyperion
  * SAP
- from a DB perspective & from an infrastructure layer, data warehousing DBs use a different type of architecture entirely

### OLTP vs. OLAP
- OLTP = online transaction processing
- OLAP = online analytics processing
  * example #1: for order #1010212, pull up the row of data with name, data, address, delivery state
  * example #2 (more complex): net profit for Asia Pacific & Europe region for some digital product --> pulls in large # of records --> `sum(Asia Pacific data)` and `sum(Europe data)` --> get unit cost per item per region --> get sales price of each item

## AWS DB types
- RDS (relational database service) = OLTP
  * SQL
  * MySQL
  * PostgreSQL
  * Oracle
  * AuroraDB
  * MariaDB
- DynamoDB = NoSQL
- Redshift = OLAP
- Elasticache = in-memory caching
  * Memcached
  * Redis
