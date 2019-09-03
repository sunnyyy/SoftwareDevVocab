# RDS = Relational Database Service

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
  * do __*NOT*__ need pre=defined table structure
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

## Backup / Multi-AZ / Read Replicas
- AWS has 2 types of backups
  * automated backups
  * DB snapshots
- auto-backups
  * let you recover your DB to any point in time within a retention period (1 - 35 days)
  * you can do a point in time recovery down to a second
  * implementation:
    - takes a full daily snapshot
    - will store transaction logs throughout the day
    - when you do a recovery, AWS will first choose the most recent daily backup, then apply transaction logs relevant to that day
  * backup data is stored in S2 & you get free storage equal to the size of your DB
    - --> if you have an RDS instance of 10 GB, you get 10 GB worth of backup storage
  * backup is taken within a defined wihndow
    - during backup window, storage I/O may be suspended
    - you may experience higher latency for reads
- DB snapshots
  * done manually (aka user-initiated)
  * stored even after you delete the RDS instance (unlike for auto-backups)
- restoring backups
  * when you resore either an auto-backup or a manual snapshot, the restored version of the DB will be a new RDS instance with a new DNS endpoint
  * e.g. `original.us-east-1.rds.aws.com` --> `restored.us-east-1.rds.aws.com`
- encryption at rest
  * supported for MySQL, Oracle, SQL server, PostgreSQL, MariaDB, Amazon Aurora
  * done via KMS
  * once your RDS instance is encrypted, the data stored at rest in the underlying storage is encrypted, as well as its auto backups, read replicas, and snapshots
  * presently, you can't encrypt an existing DB instance
  * to encrypt an existing DB, you have to first create a snapshot, make a copy of the snapshot, & encrypt the copy (during setup)
- multi-AZ
  * you have an exact copy of prod DB in another AZ, and AWS handles the replication (sync'ing) for you
  * in the event of a planned DB maintenance, DB instance failure, or AZ failure, Amazon RDS will auto-failover to standby RDS instance so that the DB resumes quickly without manual admin intervention
  * e.g. if you have an RDS instance with DNS `myRds.us-east-1a.rds.aws.com` and a standby RDS instance `myRds.us-east-1b.rds.aws.com`, the DNS endpoint will be auto-switched if AWS discovers that RDS instance #1 is down
  * for disaster recovery only!
  * __*NOT*__ for improved performance --> for that, use read replicas
  * includes:
    - SQL server, Oracle, MySQL server, PostgreSQL, MariaDB

## Read Replicas
- read replica = a read-only copy of your prod DB
- achieved using an async replication from the primary RDS instance to the read replica
- used for very read-heavy DB workloads
- available for
  - MySQL server
  - PostgreSQL
  - MariaDB
  - Amazon Aurora
- used for scaling out
- __*NOT*__ used for disaster recovery
- details:
  - must have auto-backups enabled to deploy a read replica
  - up to 5 read replica copies of any DB
  - you can have read replicas of read replicas --> but watch out for latency!
  - each read replica will have its own DNS endpoint
  - a read replica _can_ be in a different AZ
  - you _can_ make a read replica of a multi-AZ source DB
  - you _can_ have a read replica in a 2nd region
  - read replicas can be promoted to be their own DB __*BUT*__ this will break the replication
    - useful for if you want to run an experiment on your data without affecting read data
  
