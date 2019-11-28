# DynamoDB

## Quick facts
- NoSQL database
- consistent, single-digit millisecond latency at any scale
- supports both document & key/value data models
- flexible data model
- reliable performance
- example use cases: mobile, web, gaming, ad-tech, IoT

## Details: implementation
- stored on SSD storage
- spread across 3 geographically-distinct data centers
- choice of 2 consistency models
  * __eventual consistent reads__ (default)
    - consistency across all copies of data is usually reached within a second
    - repeating a read after a short time should return the updated data
    - best read performance
  * __strongly consistent reads__
    - returns a result that reflects all writes that received a successful response prior to the read
- made up of...
  * tables
  * items or "records" (rows in a table)
  * attributes (columns in a table)
  * _supports key-value + document data structures_
    - key = name of data, value = data itself
    - documents can be written in JSON, HTML, XML

## Primary keys
- DynamoDB stores / retrieves data based on a __primary key__
- 2 types:
  * __partition key__ = unique attribute (e.g. user ID)
    - the value of the partition key is input to an internal hash function which determines the _partition_ (aka physical location) on which the data is stored
    - if you're using the partition key as your primary key, then no two items can have the same partition key
  * __composite key__ = partition key + sort key (used together)
    - e.g. same user posting many times on an online forum
      * partition key = user ID
      * __sort key__ = timestamp of a post
    - 2 items may have the same partition key, but they must have a different sort key
    - all items with the same partition key are stored together, then sorted according to the sort key value
    - allows you to store multiple items with the same partition key

### Example

no. 1:
```json
{
	"UniqueID" : 11234,		// primary key
	"FirstName" : "Adam",
	"LastName" : "Brown",
	"Phone" : 123-2345
}
```

no.2
```json
{
	"UniqueID" : 02143,						// partition key
	"FirstName" : "Ahmed",
	"LastName" : "Bhatt",
	"TimeStamp" : "2010-10-10 10:10:10",	// sort key
}
```

# DynamoDB: Access Control
- managed using IAM
- you cna create an IAM user within your AWS account, with specific permissions to access & create DynamoDB tables
- you can create an IAM role that enables you to obtain temporary access keys, which can be used to access DynamoDB
- you can also use a special __IAM condition__ to restrict user access to only their own 
  * e.g. a mobile gaming app has millions of users ⟶ users need to access their own high scores without viewing anyone else's data
  - add an IAM "condition" parameter `dynamodb:LeadingKeys` to allow access only to items where the partition key value matches their user ID

### Example
```json
{
	"Sid" : "AllowAccessToOnlyItemsMatchingUserId", // statement identifier
	"Effect" : "Allow",
	"Action" : [
		"dynamodb:GetItem",
		"dynamodb:PutItem",
		"dynamodb:UpdateItem",
	],
	"Resource" : [
		"arn:aws:dynamod:eu-west-1:123451234512345:table/HighScores"
	],
	"Condition" : {									// allows users to access
		"ForAllValues:StringEquals" : {				// only the items where the
			"dynamodb:LeadingKeys" : [				// partition key value
				"${www.mygame.com:user_id}"			// matches their user ID
			],
			"dynamodb:Attributes" : [
				"UserId",							// defines the attributes
				"GameTitle",						// that the policy applies to
				"TopScore",
			]
		}
	}
}
```

# DynamoDB: Indexing
- indexes enable fast queries on specific data columns
- give you a different view of your data, based on alternative partition/sort keys

| local secondary index                 | global secondary index  |
|---------------------------------------|-------------------------|
| must be created when table is created | can create at any time  |
| same partition key                    | different partition key |
| different sort key                    | different sort key      |

# DynamoDB: Scan vs. Query API calls

## Query
- __*finds items in a table using only the primary key attribute*__
- you provide the primary key name + a distinct value to search for
  * e.g. "select an item where the user ID is equal to 212" ⟶ will select all the attributes for that item
- results are always sorted by the sort key
  * by default, in ascending order (1, 2, 3, 4) & by ascending ASCII char code values
  * you can reverse the order by setting the `ScanIndexForward` parameter to false
- can use an optional sort key name & value to refine the results
  * e.g. if your sort key is a timestamp, you can refine the query to only select items with a timestamp of the last 7 days
- by default, a query returns _ALL_ the attributes, but you can use the `ProjectionExpression` parameter if you want the query to only return the specific attributes you want
  * e.g. if you only want to see the email address, instead of everything
- by default, queries are __eventually consistent__ ⟶ you need to explicitly set the query to be _strongly consistent_

## Scan
- __*looks at every item in the table*__
- by default, returns all data attributes
- use the `ProjectionExpression` parameter to refine the results ⟶ this filter is an extra step; _Scan_ dumps the entire table, then filters out the values to remove the unwanted data
- as the table grows, the scan operation takes longer
- scan operation on a large table can use up the provisioned throughput for a large table in just a single operation

### Pro tips
- query > scan
  * avoid scan operations if possible
  * design tables in a way such that you can use _Query_, _Get_, or _BatchGetItems_ APIs
- you can reduce the impact of a query or scan by setting a smaller page size
  * uses fewer read operations
  * e.g. set the page size to return 40 items
  * larger numbers of smaller operations will allow other requests to succeeed without throttling
- use parallel scans
  * by default, a scan operation processes data sequentially in 1 MB increments, returns, and then moves onto the next 1 MB of data
  * scan only looks at one partition at a time
  * if you divide a table or index into segments, you can scan each segment in parallel
  * _AVOID THIS IF_ your table already gets heavy read / write activity ⟶ separate your scan operations from mission-critical traffic

# DynamoDB: Capacity

## Provisioned throughput
- DynamoDB provisioned throughput measured in __capacity units__
- when you create your table, you specific your reqs in terms of read capacity units vs. write capacity units
  * 1x __write capacity unit__ = 1x 1 KB writes/sec
  * 1x __read capacity unit__ = 
    - 1x strongly-consistent reads of 4 KB/sec
    - _OR_ 2x eventually consistent reads of 4 KB/sec (default)
- e.g. a table with 5x read capacity units & 5x write capacity units could perform ...
  * 5x 4 KB strongly consistent reads = 20 KB/sec
  * 10x 4 KB eventually consistent reads = 40 KB/sec
  * 5x 1 KB writes/sec
- if your app reads/writes larger items, it'll consume more capacity units & cost more
- calculating read capacity units
  * e.g. your app needs to read 80 items (table rows) per second && each item = 3 KB in size && you need strongly consistent reads
    - `[size of each item] / 4 KB = 3 KB / 4 KB = 0.75`
    - round up to the nearest whole number ⟶ each read will need 1x read capacity unit per read operation
    - multiplied by the number of reads per second = 80 read capacity units required
  * for the same scenario but with eventually consistent reads, you'd need 40 read capacity units
- calculating write capacity units
  * e.g. you want to write 100 items/sec && each item = 512 bytes
    - capacity units = 512 bytes / 1 KB = 0.5 KB ⟶ round up ⟶ need 1x write capacity unit per write operation
    - multiply by # writes/sec = need 100 write capacity units

## On-demand capacity
- charges apply for reading, writing, & storing data
- with on-demand, you pay per request
  * it instantly scales up/down based on the activity of your app
  * good for unpredictable workloads, e.g. serverless
- you can switch between on-demand vs. provisioned capacity models 1x/day

| on-demand capacity        | provisioned capacity                         |
| ------------------------- | -------------------------------------------- |
| unknown workloads         | forcasted read/write capacity reqs           |
| unpredictable app traffic | predictable traffic                          |
| spiky, short-lived peaks  | traffic is consistent or increases gradually |
| pay-per-use model         |                                              

# DynamoDB: DynamoDB Accelerator (DAX)

- clustered, in-memory cache for DynamoDB tables
- benefits:
  * delivers up to a 10x read performance improvement
  * microsecond performance = millions of requests per second
  * ideal for workloads that are read-heavy or prone to bursts
  * e.g. auction apps, gaming, retail sites during Black Friday
- is a __*write-through caching service*__ = data is written to the cache + backend store at the same time
  * you point your DynamoDB API calls to the DAX cluster instead of the table
  * if the item you're querying is in the cache ("cache hit"), DAX returns the result to the app
  * if the item is not available ("cache miss"), DAX performs an __*eventually consistent*__ GetItem operation against DynamoDB (and also writes it to the cache)
- retrieval of data from DAX reduces the read load on DynamoDB tables
- may be able to reduce provisioned read capacity
- _IS **NOT** SUITABLE FOR:_
  * applications that require strongly consistent reads (since it caters only to eventually consistent reads
  * write-intensive apps
  * apps that don't perform many reads
  * apps that don't require microsecond response times

# DynamoDB: Transactions

- ACID transactions = atomic, consistent, isolated, durable
  * atomic = each transaction is a single unit
  * consistent = must leave the DB in a valid state
  * isolated = no transaction relies on another
  * durable = once a transaction has been committed (written to disk), it will not change
- e.g. financial transactions shouldn't be partially-completed; should be "permanent"
- to implement:
  * read / write multiple items across multiple tables as an all-or-nothing operation
  * check for a pre-req. condition before writing to a table

# DynamoDB: TTL (Time-to-Live)
- TTL defines an expiry time for your data, which are auto-marked for deletion in the next 48 hours
- great for removing irrelevant / old data
  * session data
  * event logs
  * temp data
- reduces cost
- TTL is expressed as epoch time
- expiration is set for X time after the session began
- when current time > TTL item will be expired / marked for deletion
- you can filter out expired items from your queries & scans

# DynamoDB Streams
- a time-ordered sequence of modifications at the item level (e.g. insert, update, delete)
- logs encrypted at rest & stored for 24 hours
- accessed using a (separate) dedicated endpoint
- by default, primary key is recorded
- before/after images can be captured
- [FLOW DIAGRAM]
- processing
  - very good for serverless (?)
  - apps can take actions based on contents
  - event source for Lambda --> Lambda polls the DynamoDB stream --> executes Lambda code based on a DynamoDB Streams event
  - [FLOW DIAGRAM]

# `ProvisionalThroughputExceededException`
- your request rate is too high for the read/write capacity you provisioned for your table
- SDK will auto-retry until requests are successful
- if you're not using the SDK, you can configure:
  - reduce request frequency
  - use __exponential backoff__
    - progressively longer waits between consecutive retries for improved flow control
    - e.g. first try 50 ms wait, then 100 ms, then 200 ms, etc.
    - if after 1 min this doesn't solve the problem, your request size may be exceeding the throughout for your read/write capacity
    - if too many __*reads*__ --> consider DAX or Elasticache
    - if too many __*writes*__ --> increase capacity

# DynamoDB Summary
- low-latency, NoSQL DB
- tebles, items (rows), attributes (cols)
- supposrts both document (?) & key-value data models
- supported formats: JSON, HTML, XML
- 2 types of primary keys
  - partition key
  - partition key + sort key
- 2 consistency models
  - strongly consistent
  - eventually consistent
- access is controlled using IAM policies
- fine-grained access control via IAM