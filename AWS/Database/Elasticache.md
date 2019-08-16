# Elasticache

## Quick facts
- what is caching?
  * storing critical pieces of data in memory for low-latency access
  * improves app performance
- Elasticache makes it easy to deploy, operate, and scale an in-memory cache in the cloud; sits between your app & DB
- benefits / use cases
  * improves performance for read-heavy workloads
    - e.g. social networking, gaming, media sharing, Q&A portals
    - cached info may include results of I/O intense DB queries ⟶ good if your DB is especially read-heavy
  * good for computationally-intensive calculations
    - e.g. recommendation engines
  * good if the data doesn't change frequently

## Types of Elasticache
- while Memcached & Redis appear similar (both are in-memory key stores) the implementation is different
- because __Memcached__ is designed as a pure caching solution with no persistence, Elasticache manages Memcached nodes as a __*pool*__ that can grow & shrink, similar to an Amazon EC2 scaling group
  * individual nodes are expendable & Elasticache provides additional capacities, e.g. automatic node replacement & auto-discovery
- because of the repetition & persistence features of __Redis__, Elasticache manages Redis as a __*relational DB*__
  * Redis Elasticache clusters are managed as stateful entities that include failovers, similar to how Amazon RDS manages DB failover

| Memcached                                   | Redis                                     |
| ------------------------------------------- | ------------------------------------------|
| widely-adopted memory object caching system | open-source in-memory key-value store     |
| simplest caching method                     | supports more commplex data structures (sorted sets, hashes, lists) |
| no multi-AZ capability                      | supports multi-AZ for cross-AZ redundancy |
| multi-threaded                              | supports parent/child replication         |
| use cases
  <br>• object caching (e.g. to offload your DB)
  <br>• large cache nodes
  <br>• multi-threaded performance with multiple cores
  <br>• scale & cache horizontally as you grow ("scale out")
| use cases
  <br>• sort / rename datasets (e.g. leaderboards)
  <br>• persistence of key store / data persistence
  <br>• run in multiple AZ's with failover
  <br>• pub / sub capabilities |

## Caching strategies
- __Lazy Loading__
  * loads the data into the cache only when necessary
  * if the data is in the cache, Elasticache returns the data to the app
  * if the data is not in the cache / has expired, returns a `null`
  * your app then fetches the data from the DB and writes the data received into the cache so that it's available next time
  * pros
    - only requested data is cached; avoids filling up cache with useless data
    - __*node failures*__ are not fatal; a new empty node will just have lots of cache misses initially
  * cons
    - cache miss penalty:
      * initial request
      * query to DB
      * writing of data to the cache
    - __*stale data*__ = if data is only updated when there's a cache miss, it can become stale
      * Elasticache doesn't automatically update if the data in the DB changes
      * __TTL = time to live__
        - specifies # seconds until the key (data) expires to avoid keeping stale data in the cache
        - Lazy Loading treats the expired key as a cache miss & causes the app to retrieve the data from the DB again and re-writes it with a new TTL
        - doesn't eliminate stale data but helps avoid it
- __Write-Through__
  * adds / updates the data to the cache whenever data is written to the DB
  * pros
    - data in the cache is never stale
    - users are generally more tolerant of additional latency when updating data vs. when retrieving it
  * cons
    - write penalty
      * every write involves a write to the cache as well as to the DB
      * if a node fails & a new one is spun up, data is missing until added / updated in the DB
        - can mitigate by implementing Lazy Loading in along with Write-Through)
      * wasted resources if most of the data is never read

## Exam tips
- you'll likely get a scenario where a particular DB is under a lot of stress / load & you'll be asked which service you should use to alleviate this
- Elasticache = good if your DB is especially read-heavy + is not prone to frequent changes
- Redshift = good if your DB is feeling stress from management running OLAP (Online Analytical Processing) transactions on it