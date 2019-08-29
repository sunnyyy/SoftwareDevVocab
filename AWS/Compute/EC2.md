# EC2 = Elastic Compute Cloud

## Quick facts
- provides resizable compute capacity in the cloud
- reduces time to obtain/boot new servers (vs. historically, physcial servers)
- quickly scale capacity ↕
- charges you only for the capacity you use
- failure-resilient

## Plans
- __on demand__
  * fixed rate by hour/second
  * no commitment
- __reserved (1-3 year contract)__
  * capacity reservation at discount
  * good for:
    - apps with steady state or predictable usage
    - apps that require reserved capacity (e.g. something that absolutely can't go offline)
  * 3 types: standard, convertible, scheduled
    - __*standard RI*__ = up to 75% off
    - __*convertible RI*__ = up to 54% off; you can change the attributes of this RI so long as you end up creating RIs of equal or greater value
    - __*scheduled RI*__ = available to launch within the time window you reserve; good for predicable, recurring schedule
- __spot__
  * bidding $ for capacity
  * good for:
    - flex start/end times
    - tasks that are very compute-heavy, and so can only be done during off-hours when $$ is cheaper
    - e.g. parsing genetic code at 4 AM for research
  * _NOTE: if Amazon cuts you off mid-hour, you don't pay; if you cut yourself off, you pay for full hour_
- __dedicated hosts__
  * can be purchased on-demand, but if you purchase it as a reserved RI it can be up to 70% off
  * physical server for you only
  * good for:
    - if you can't use multi-tenant virtualization, e.g. for compliance with healthcare laws
    - if you have an existing server-bound license and want to continue to use that

## Types
| type | purpose                               | example |
|------|---------------------------------------|---------|
| F1   | field-programmable gate array         | • genomics research<br>• financial analytics<br>• real-time video processing<br>• big data |
| I3   | high speed ([IOPS][IOPS])             | • NoSql DBs<br>• data warehousing |
| G3   | graphics intensive                    | • video encoding<br>• 3D app streaming |
| H1   | high-disk thoroughput                 | • [MapReduce](https://en.wikipedia.org/wiki/MapReduce)-based workloads<br>• distributed file systems |
| T2   | low-cost/general purpose              | • web servers<br>• small DBs |
| D2   | dense storage                         | • file servers<br>• data warehousing<br>• [Apache Hadoop](https://en.wikipedia.org/wiki/Apache_Hadoop) |
| R4   | memory optimized ("RAM")              | • memory-intensive apps<br>• DBs |
| M5   | general purpose (main choice)         | • app servers |
| C5   | compute optimized                     | • CPU-intensive apps<br>• DBs |
| P3   | graphics/general purpose GPU ("pics") | • ML<br>• Bitcoin mining |
| X1   | memory optimized ("xtreme memory")    | • [SAP HANA](https://en.wikipedia.org/wiki/SAP_HANA)<br>• [Apache Spark](https://en.wikipedia.org/wiki/Apache_Spark) = general-purpose cluster-computing framework; implicit data parallelism and fault tolerance |

## 

###### Notes:
[IOPS]: https://en.wikipedia.org/wiki/IOPS
- [IOPS](https://en.wikipedia.org/wiki/IOPS) = IO operations per second; used to measure performance for compute resources
- [MapReduce](https://en.wikipedia.org/wiki/MapReduce) = model to process and generate big data sets with a parallel, distributed algorithm on a cluster
- [Apache Hadoop](https://en.wikipedia.org/wiki/Apache_Hadoop) = network of many computers to solve problems involving massive amounts of data and computation
- [SAP HANA](https://en.wikipedia.org/wiki/SAP_HANA) = in-memory, column-oriented, relational database management system
- [Apache Spark](https://en.wikipedia.org/wiki/Apache_Spark) = general-purpose cluster-computing framework; implicit data parallelism and fault tolerance

