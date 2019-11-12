# Aurora

## Quick facts
- relational DB engine compatible with MySQL, PostgreSQL

## Details
- start with 10 GB --> autoscales in 10 GB increments --> up to 64 TB
- compute resources can scale up to 32 vCPUs + 244 GB of memory
- 2 copies of your data is contained in each AZ * with minimum of 3 AZ's = 6+ copies of your data
- sync'ing
  - designed to handles loss of up to ...
    - ... 2 copies of data without affecting write availability
    - ... 3 copies of data without affecting read availability
  - storage is __self-healing__, aka data bocks & disks are continuously scanned for errors & auto-repaired
- backups
  - always enabled
  - do not impact DB performance
- snapshots
  - no impact on performance
  - can be shared with other AWS accounts

## Types of replicas

| Aurora replicas     | MySQL replicas         |
|---------------------|------------------------|
| up to 15 copies     | up to 5 copies         |
| automated failovers | NO automated failovers |