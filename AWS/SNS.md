# SNS = Simple Notification Service

## Quick facts
- scalable service to send push notifications
- supported formats
  - push notifications to iOS, Android, FireOS, Windows, plus Android devices in China (via Baidu Cloud Push)
  - SMS texts
  - email
  - SQS queues
  - any HTTP endpoint
- can trigger Lambda functions
  - if a message is published to SNS topic && if a Lambda function subscribed to it, the Lambda is invoked with a payload of hte published message as input
  - the Lambda can then manipulate the info in the message, publish a message to another SNS topic, or send the message to other AWS services
- push-based (no polling)
  - follows publish/subscribe ("pub-sub") model where users subscribe to topics

## SNS Topics
- __topic__ = "access point" for recipients to dynamically subscribe to copies of the same notification
- one topic can deliver to multiple endpoint types (e.g. iOS + Android + SMS texts)

## Details
- highly scalable, flexible, cost-effective (pay-as-you-go)
- to prevent messages from being lost, all messages published to SNS are stored redundantly across multiple AZ's
- flexible message delivery over many transport protocols
- web-based console = simple point-and-click interface
- pricing
  - $0.50 / 1 million SNS requests
  - $0.06 / 100,000 SNS deliveries over HTTP
  - $0.75 / 100 SNS deliveries over SMS text
  - $2.00 / 100,000 SNS deliveries over email

___

# SES (Simple Email Service)

## Quick facts
- scalable email service designed to help marketing teams & devs send marketing, notification, and transactional emails to customers
- pay-as-you-go
- can also be used to receive emails
  - incoming mail delivered automatically to S3 bucket
  - can trigger Lambda functions & SNS notifications
- use cases
  - automated emails: purchase confirmations, shipping notifications, order status updates
  - marketing emails: ads, newsletters, special offers

## SES vs. SNS
|                    | SES           | SNS                     |
|------------------- | ------------- | ----------------------- |
| __what is it?__    | email service | pub/sub message service |
| __format__         | just email    | email, SMS, HTTP, SQS   |
| __can trigger...__ | Lambda or SNS | Lambda                  |
| __pros__           | can handle incoming or outgoing email | can replicate message & push to many formats |
| __requires__       | just the user's email address | consumer must subscribe to the topic ("pub/sub model") |