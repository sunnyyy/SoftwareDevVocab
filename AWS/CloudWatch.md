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

### Metric granularity
  * depends on the AWS service
    - many default metrics are 1 min
    - EC2 default metrics are 5 min
  * __*minimum*__ custom metric = 1 min


### Data retention
- you can retrieve data with `GetMetricStatistics` API or with 3P tools offered by AWS partners
- you can store log data in CloudWatch logs for as long as you want
  * default: indefinitely
  * you can change the retention for each log group at any time
- you can retrieve data from any terminated EC2 or Elastic Load Balancer instance after its termination

### On-premises (option; often for legal compliance)
- monitoring not restricted to just AWS resources
- just need to download / install the SSM agent + CloudWatch agent

___

# CloudWatch vs. CloudTrail vs. Config
- __CloudWatch__ monitors _performance_ ⟶ e.g. CPU ⟶ can be custom or default
- __CloudTrail__ monitors _API calls_ in the AWS platform ⟶ e.g. who provisioned it ⟶ used for auditing
- __AWS Config__ records the state of your AWS env + can notify you of changes