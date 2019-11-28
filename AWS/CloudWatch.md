# CloudWatch

## Quick facts
- for monitoring performance
- can monitor most AWS services + apps that run on AWS
- NOTE: not the same as CloudTrail, which audits API calls
- use cases
  - create ___dashboards___
  - set ___alarms___ to notify you when thresholds are hit
  - create ___events___ based on ___state changes___ in your AWS services
  - aggregate, monitor, and store ___logs___

## Details
- metric granularity:
  * default EC2 monitoring is 5 min; detailed is 1 min
  * default monitor granularity varies per AWS service
  * most granular is 1 min for detailed monitoring
- default metrics:
  * physical, host-level metrics: CPS, network, disk
  * __NOTE__: cannot see storage remaining on virtual disk
- custom metrics:
  * RAM utilization
  * (?) any sort of metric more granular than 1 min has to be specially config'ed, and even then it'll aggregate based on X time period, where X = level of granularity (?)
- data retention:
  * metrics stored indefinitely unless you change retention period
  * you can retrieve data from any terminated EC2 instance after termination
- use `GetMetricStatistics` (or 3P tools) to get data
- you can create alarms to monitor any CloudWatch metric
  * example use cases
    - e.g. EC2 CPU utilization
    - e.g. Elastic LB latency
    - e.g. charges on AWS bill
  * you can set appropriate thresholds
  * you can set what actions you want to be taken, e.g. get SNS notification
- CloudWatch can even be used on-premise ⟶ just download & install the SSM agent + CloudWatch agent
  * default EC2 monitoring is 5 min; detailed is 1 min
- CLI
  - you need to set up access in IAM
  - list of basic commands: https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/index.html

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

-----
# CloudWatch Labs

## Lab: Set up RAM utilization metrics in CloudWatch
- use us-east-1 (N. Virginia) ⟶ first to get launches, but also most likely to go down
- go to IAM in Console ⟶ create new role "CloudwatchEC2"
  * ⟶ select "EC2" under "service that will use this role"
  * ⟶ select "CloudwatchFullAccess" IAM policy (could probs be even more restrictive)
- go to EC2 in Console ⟶ click "launch instance"
  * ⟶ config details = "Amazon Linux 2 AMI" ⟶ # instances = 1 ⟶ leave everything else as default ⟶ use the IAM role you just made
  * ⟶ add bootstrap script (provided in lecture)
  * ⟶ add tags
  * ⟶ add / create new security group ⟶ make SSH & HTTP open to the world
  * ⟶ review / launch
  * ⟶ create new key pair
  * ⟶ copy EC2's public IP address
  * (wait)
  * ⟶ SSH into EC2 instance ⟶ `ssh <user>@<IP> -i <myKeyPair>.pem`
  * ⟶ `chmod 400 <myKeyPair>.pem` + `sudo su` + `ls` ⟶ should list CloudWatchClient.pem
- go to CloudWatch in Console ⟶ click "browse metrics"
  * ⟶ go to EC2 ⟶ "per-instance metrics" (default metrics) (?)
  * ⟶ go back to terminal
    - `put instance ...` (?)
    - `put instance ...` (?)
  * only a single data point written
  * refresh after some time ⟶ you should now have a graph of memory usage
  * since we haven't enabled detailed monitoring, it's only after ever 5 mins evne though the script says every min
