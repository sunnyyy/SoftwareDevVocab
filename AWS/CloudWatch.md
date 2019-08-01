# CloudWatch

## Quick facts
- monitoring service to monitor your AWS resources & the apps you run on AWS
- examples:
  * compute
    - Autoscaling groups
    - Elastic Load Balancer
    - Route53 health checks
  * storage & content delivery
    - Elastic Beanstalk volumes
    - storage gateways
    - CloudFront
  * database & analytics
    - DynamoDB
    - Elasticache nodes
    - RDS (relational database system ???) instances
    - elastic MapReduce job flows
    - RedShift
  * other
    - SNS topic
    - SQS queues
    - opsworks ???
    - CloudWatch logs
    - estimated charges on your AWS bill

## Details
- default metrics:
  * physical, host-level metrics: CPS, network, disk
  * __NOTE__: cannot see storage remaining on virtual disk
- custom metrics:
  * RAM utilization
  * default EC2 monitoring is 5 min; detailed is 1 min
  