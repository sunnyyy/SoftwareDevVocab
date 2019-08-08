# EC2 = Elastic Compute Cloud

## Details
- reduces time to obtain/boot new servers
- quickly scale capacity ↕

## Plans
- on demand
  * fixed rate by hour/second
  * no commitment
- reserved
  * capacity reservation at discount
  * 1-3 year terms
  * 3 types: standard, convertible, scheduled
- spot*
  * bidding $ for capacity
  * good for flex start/end times, e.g. 4 AM parsing of genetic code
  * NOTE: if Amazon cuts you off mid-hour, you don't pay; if you cut yourself off, you pay for full hour
- dedicated hosts
  * physical server for you only, e.g. to comply w/ healthcare reqs

## Types
| type | purpose                               | example |
|------|---------------------------------------|---------|
| F1   | field-programmable gate array         |         |
| I3   | high speed (IOPS ?)                   |         |
| G3   | graphics intensive                    |         |
| H1   | high-disk thoroughput                 |         |
| T2   | low-cost/general purpose              |         |
| D2   | dense storage                         |         |
| R4   | memory optimized ("RAM")              |         |
| M5   | general purpose (main choice)         |         |
| C5   | compute optimized                     |         |
| P3   | graphics/general purpose GPU ("pics") |         |
| X1   | memory optimized ("xtreme memory")    |         |

___

# EBS = Elastic Block Store

## Details
- create storage & attach to EC2 instances
- for: creating file sys, run database, etc.
- by availability zone
- auto-replicated for safety

## Types
- SSD = solid state drive
  * general-purpose SSD (GP2)
    - balance price vs. performance
    - 10000 IOPS ± 3000 IOPS
  * provisioned IOPS SSD (IO1)
    - intensive I/O apps (e.g. NoSQL, large relational databases, etc.)
    - 10000+ IOPS (up to 20000 IOPS ?)
- magnetic / HDD = hard disk drive
  * throughput-optimized HDD (ST1)
    - good for big data, data warehouse, log processing
    - cannot be boot volume (akin to D drive in Windows, not C drive)
  * cold HDD (SC1)
    - lowest-cost, for infreq access (e.g. file server)
    - not boot volume
  * magnetic (standard) ⟶ legacy!
    - lowest $ per GB, for infreq access
    - can be boot volume

