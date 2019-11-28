# RDS = Relational Database Service

## Quick facts
- RDS (relational database service) = OLTP
  * SQL
  * MySQL
  * PostgreSQL
  * Oracle
  * AuroraDB
  * MariaDB
- runs on VM's, but you can't log in
- patching the RDS OS is Amazon's responsibility
- is NOT serverless (with the exception of Aurora serverless)

## Backups
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

## Encryption at rest
- done via KMS
- once your RDS instance is encrypted, the data stored at rest in the underlying storage (+ ___all___ its auto backups, read replicas, and snapshots) is encrypted
- to encrypt an existing DB, you have to first create a snapshot, make a copy of the snapshot, & encrypt the copy (during setup)
- available for:
  - SQL server
  - Oracle
  - MySQL server
  - PostgreSQL
  - MariaDB
  - Amazon Aurora

## Multi-AZ
- exact copy of a prod DB in another AZ (auto sync'ed)
- for disaster recovery only!
  - in emergency, Amazon RDS will auto-failover to the standby RDS instance
  - e.g. if you have an RDS instance with DNS `myRds.us-east-1a.rds.aws.com` and a standby RDS instance `myRds.us-east-1b.rds.aws.com`, the DNS endpoint will be auto-switched
- example use cases:
  - planned DB maintenance
  - DB instance failure --> can be simulated by rebooting the RDS instance
  - AZ failure
- available for:
  - SQL server
  - Oracle
  - MySQL server
  - PostgreSQL
  - MariaDB

## Read Replicas
- read-only copy of your prod DB (auto async'ed)
- for very read-heavy DB workloads --> for scaling out only!
- details:
  - must have auto-backups enabled
  - max 5 read replicas of any DB
  - each read replica will have its own DNS endpoint
  - can have read replicas of read replicas --> but watch out for latency!
  - can be multi-AZ
  - can be in a 2nd region --> but watch out for latency!
  - read replicas can be promoted to be their own DB ___BUT___ this will break the replication
    - useful for if you want to run an experiment on your data without affecting read data
- available for:
  - MySQL server
  - PostgreSQL
  - MariaDB
  - Amazon Aurora

