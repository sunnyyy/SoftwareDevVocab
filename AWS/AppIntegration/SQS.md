# SQS = Simple Queue Service

## Quick facts
- web service with message queue that can be used to store messages awaiting process
- used in _distributed_ systems
- enables web service apps to quickly / reliably queue messages that one app component generates to be consumed by another component
- pull-based system
- messages contain up to 256 KB of text
- messages can be kept 1 min ⟷ 14 days (default = 4 days)
- guarantees messages processed at least 1x

### ex: meme website
user uploads pic to __S3__ ⟶ triggers __Lambda__ ⟶ __SQS__ (waiting to be processed) ... ⟵ __EC2__ (pulls from SQS) ⟶ sent back to __S3__

### ex: travel website
user searches ⟶ search goes to __EC2__ ⟶ search gets packaged in __SQS__ ... ⟵ EC2 (looking for job) ⟶ calls the APIs of various airlines ⟶ results sent back to first EC2 instance and then back to user

## Benefits of SQS
- if you lose an EC2 instance, you won't lose the job
- using SQS, you can decouple components of an app so they run independently
- any component in a distributed app can store messages in SQS
  - any component can later retrieve messages programmatically via SQS API
- queue acts as buffer between components producing data & components receiving data for processing
  - aka queue resolves issue if producer produces work more quickly than consumer can process
  - resolves issues if producer / consumer are only intermittently connected
- __pro tip:__ configure auto-scaling group
  - if queue goes over X messages, add new instances
  - vice-versa for cooldowns

## 2 types of SQS queues
- __standard (default)__
  - nearly unlimited transactions/sec
  - guarantee message delivered at least 1x
  - due to highly-distributed architecture, sometimes 1+ copy of message delivered out of order -- e.g. 1, 2, 5, 3, 3, 4
  - provide best-effort ordering (aka generally delivered in same order sent)
- __FIFO (first in, first out)__
  - message delivered 1x & remains available until consumer processes & deletes it
  - duplicates are _NOT_ introduced
  - supports message groups (multiple ordered message groups within single queue)
  - limited to 300 TPS

## Visibility timeout
- amount of time a message is invisible in the queue after a reader picks up the message
- if the job is processed before timeout expires, message will be deleted from queue
- if job is _NOT_ processed before timeout, message will be visible again and another reader will process it
- default timeout is 30 secs; you can manually increase it
- max timeout is 12 hours

## Polling (retrieving messages) in SQS
- regular __short polling__ returns immediately, even if queue being pulled from is empty
- __long polling__ doesn't return response until a message arrives in the queue, or long poll times out ⟶ can save $$

## SQS delay queues
- postpone delivery of new messages to a queue for a # of seconds
- messages sent to the delay queue are invisible to consumer during delay period
- delay = 0 seconds (default) to 900 seconds (15 mins)
- for standard queues, changing the settings wouldn't affect delay of existing messages, only of new messages
- for FIFO, changing the settings would affect the delay of messages already in the queue
- when to use?
  - large distribution apps, which may need a delay in processing
  - when delay is needed for entire queue
  - e.g. a few seconds delay may allow updates to a DB for stocks / ETFs, before sneding a notification to a customer
  - e.g. confirming an online sales transaction

## large SQS messages
- large message = 256 KB - 2 GB
- requires:
  - S3 (bucket) to store message
  - AWS SDK for Java, for API, for S3 bucket + job operations
  - __Amazon SQS extended client library for Java__
    - specifies whether messages are always stored in S3, or only those messages that are over 256 KB
    - can send a message that references a message object stored in S3
    - can get a message object from S3
- can NOT be done via:
  - AWS CLI
  - AWS console / SQS console
  - SQS API
  - any other AWS SDK

## Exam tips
- SQS = distributed message queueing system
- allows decoupling of app components
- pull-based
- types of queue
  - standard queue = best effort ordering; message delivered 1+ x
  - FIFO queue = strinct ordering; message delivered 1x; no duplicates ⟶ e.g. banking
- visibility timeout
  - default is 30 secs; can increase
  - max is 12 hours
- polling
  - short polling = returns immediately even if no message
  - long polling = pulls periodically & only returns when message in queue or timeout