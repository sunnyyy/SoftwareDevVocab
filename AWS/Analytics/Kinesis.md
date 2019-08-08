# Kinesis

## Quick facts
- __streaming data__ = data generated continuously by thousands of data sources
  - typically send in data simultaneously & in small sizes (KB size)
  - examples:
    - purchases on Amazon
    - game data during play
    - social network data
    - geospatial data on Uber
    - iOT sensors
- you can send streaming data to __Kinesis__ platform to load / analyze the data
- 3 Kinesis services:
  - Kinesis Streams
  - Kinesis Firehose
  - Kinesis Analytics

## Kinesis Streams
- receives data from producers
  - data retention: 24 hours (default) up to 7 days
  - data stored in __"Shards"__
    - 1 Shard has 5 TPS for reads ⟶ max total read rate of 2 MB/sec
    - 1 Shard has 100 TPS for writes ⟶ max total write rate of 1 MB/sec (including partition keys)
  - total data capacity = `SUM(`capacity of # shards`)`
- data can then be sent to EC2 consumers ⟶ then DynamoDB, S3, EMR, or Redshift

## Kinesis Firehose
- receives data from producers & analyzes it direcly with Lambda ⟶ then sent to S3 ⟶ then Redshift or Elastic Search Cluster
- more automated than Kinesis Streams (no need to manually add Shards)
- no automatic data retention
- can analyze data automatically with Lambda

## Kinesis Analytics
- run SQL queries on data in Kinesis Firehose or Kinesis Streams
- store the SQL data in S3, Redshift, or Elastic Search Cluster