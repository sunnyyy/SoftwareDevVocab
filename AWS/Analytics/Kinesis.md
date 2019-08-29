# Kinesis

## Quick facts
- __streaming data__ = data generated continuously by thousands of data sources
  * typically send in data simultaneously & in small sizes (KB size)
  * examples:
    - online purchases on a big website such as Amazon
    - stock prices
    - game data during play
    - social network data
    - geospatial data on Uber
    - iOT sensors
- you can send streaming data to __Kinesis__ platform to load / analyze the data
- 3 Kinesis services:
  * Kinesis Streams
  * Kinesis Firehose
  * Kinesis Analytics

## Kinesis Streams
- receives data from producers ⟶ stores data in __"Shards"__ ⟶ data can then be sent to __*EC2 consumers*__ ⟶ then sent to storage
  * EC2 consumers would do the computing (e.g. sentiment analysis)
  * the results of any computing would then be stored in DynamoDB, S3, EMR, Redshift, etc.
- total data capacity = `SUM(`capacity of # shards`)`
  * 1 Shard has 5 TPS for reads ⟶ max total read rate of 2 MB/sec
  * 1 Shard has 100 TPS for writes ⟶ max total write rate of 1 MB/sec (including partition keys)
- data retention: 24 hours (default) up to 7 days

## Kinesis Firehose
- receives data from producers & analyzes it direcly with Lambda ⟶ then sent to S3 ⟶ then Redshift or Elastic Search Cluster
- more automated than Kinesis Streams (no need to manually add Shards)
- no automatic data retention
- can analyze data automatically with Lambda

## Kinesis Analytics
- run SQL queries on data in Kinesis Firehose or Kinesis Streams
- store the SQL data in S3, Redshift, or Elastic Search Cluster

-----
# Kinesis Labs

## Lab: Kinesis Stream
- in CloudFormation, create a new stack ⟶ specify Amazon S3 template URL ⟶ give it a stack name ⟶ choose default options ⟶ click "create" CloudFormation template ⟶ wait 10-15 mins
- click on "outputs" ⟶ see simulation of web traffic
- go to EC2 (which in this case is both data producer & consumer)
- go to Kinesis ⟶ Kinesis Streams ⟶ go to the stream you created ⟶ "details tab" ⟶ "nomitoring" tab
- go to DynamoDB (where data is stored) ⟶ click "tables" ⟶ click "items" ⟶ see individual data
- delete stack in CloudFormation to end lab (and avoid charges)
