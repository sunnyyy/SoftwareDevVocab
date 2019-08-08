# EBS = Elastic Block Storage

## Quick facts
- create storage + attach to EC2 instances
- once attached, you can: creating file sys, run database, etc.
- by availability zone
- auto-replicated for safety

## Types
- __SSD = solid state drive__
  * __*general-purpose SSD (GP2)*__
    - balance price vs. performance
    - 10000 IOPS ± 3000 IOPS
  * __*provisioned IOPS SSD (IO1)*__
    - intensive I/O apps
    - e.g. NoSQL, large relational databases, etc.
    - 10000+ IOPS (up to 20000 IOPS per volume)
- __magnetic / HDD = hard disk drive__
  * __*throughput-optimized HDD (ST1)*__
    - good for big data, data warehouse, log processing
    - not boot volume ("boot volume" is like C drive on a Windows; "non-boot volume" is like a D drive in Windows)
  * __*cold HDD (SC1)*__
    - lowest-cost, for infreq access (e.g. file server)
    - not boot volume
  * __*magnetic (standard) ⟶ legacy!*__
    - lowest $ per GB, for infreq access
    - can be boot volume