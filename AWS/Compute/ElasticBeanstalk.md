# Elastic Beanstalk

## Quick facts
- fastest / simplest way in AWS to deploy your app onto server platforms
- (is basically a provisioning service like CloudFormation, except CloudFormation uses JSON & templates whereas ElasticBeanstalk uses the GUI)
- when you upload code to Beanstalk, it'll handle:
  - deployment
  - capacity provisioning
  - load balancing
  - auto-scaling
  - app health
- also includes:
  - updates
  - monitoring/metrics --> integrated with CloudWatch & X-Ray
  - health checks
- you can retain full control of the underlying AWS resources powering your app (e.g. the EC2 instance) or have Beanstalk do that for you
- you only pay for the AWS resources required to store / run your apps (e.g. EC2 instances & S3 buckets)

## Details
- languages supported:
  - Java
  - .NET
  - PHP
  - Node.js
  - Python
  - Ruby
  - Go
  - Docker
- server platforms supported:
  - Apache Tomcat
  - Nginx
  - Passenger
  - IIS
- you can customize a Beanstalk environment using config files
  - examples:
    - define packages to install
    - create Linux users / groups
    - run shell commands
    - specify services to enable / configure your load balancer
    - etc.
  - __*config files details:*__
    - written in __YAML__ or __JSON__
    - files must have __`.config`__ extension
    - files must be saved in a directory called __`.ebextensions`__ at the __top-level directory__ of your app code bundle

## Deployment policies
- __all at once__
  - deploys new version simultaneously to all instances
  - all instances out of service during deployment --> complete outage
  - OK for dev or test envs; not ideal for mission-critical prod systems
  - if update fails, you have to roll back by re-deploying the original version to all instances
- __rolling__
  - deploys new version in batches
  - each batch is out of service during deployment --> env capacity is reduced by the # instances in a batch
  - not ideal for performance-sensitive systems
  - if update fails, you need to perform an addtional rolling update to roll back changes
- __rolling with additional batch__
  - launches additional batch of instances
  - deploys new version in batches --> maintains full capacity during deployment
  - if update fails, you need to perform an additional rolling update to roll back changes
- __immutable__
  - deploys new version to fresh group of instances in their own auto-scaling groups
  - when these new instances pass health checks, they're moved to your auto-scaling group and your old instances are terminated
  - maintains full capacity during deployment --> impact of failed update is far less
  - preferred for mission-critical prod systems
  - rollback process only requires terminating the new instance & new auto-scaling group

## RDS (Relational Database Services) + Beanstalk

Elastic Beanstalk supports 2 ways of integrating RDS
1. launch RDS instance from within Beanstalk console
  - pros:
    - quick / easy to add your database & get started
  - cons:
    - when you terminate the Beanstalk environment, the database will also be terminateinated
    - OK for Dev / Test environments but _NOT_ for Prod!
2. launch RDS outside Beanstalk, directly from RDS console
  - pros:
    - more flexibility
    - wider choice of database types
    - allows connection from multiple Beanstalk environments to the same RDS database
    - you can tear down the app stack without impacting the database
    - suitable for Prod environments
  - 2 extra config steps:
    1. you must add an additional security group to your Beanstalk env's auto-scaling group
    2. you'll need to provide connection info to your app servers (endpoint, password) using Beanstalk's _env properties_ (?)

## Docker containers + Beanstalk
- Elastic Beanstalk supports deployment of Docker containers, which are self-contained and include all config info + software your web app needs
- you can either deploy ...
  1. a single Docker container
    - run a single Docker container on an EC2 instance provisioned by Elastic Beanstalk
  2. multiple Docker containers
    - use Elastic Beanstalk to build an ECS cluster, and deploy multiple docker containers on each instance
- to deploy your code, upload a `zip file` with your code bundle to Elastic Beanstalk
  - code can be uploaded directly from your local machine
  - code can be linked from public S3 bucket
- to upgrade your code, upload a new version of the zip file
- it's possible to store your code in CodeCommit, but you'd have to use the Elastic Beanstalk CLI to do that

-----

# Labs

## Lab: deploying apps with Elastic Beanstalk
- deploy PHP & Apache stack
- console --> Compute: Elastic Beanstalk --> choose N. Virginia region --> make new web app --> enter name --> upload
- v.1: upload code to S3 bucket --> wait 5 mins --> click link (?)
- updates: Dashboard --> managed updates (e.g. auto-updates to Node.js) --> config tab (?)

## Lab: deploying an update in Beanstalk
- console --> Compute: Elastic Beanstalk --> go to your app
- v.2: upload v.2 to "App Versions"
- click "All Apps" --> go back to Dashboard --> click "Config" tab --> env --> config --> "Modify rolling deployments" --> all-at-once (just for this lab) --> deploy
- now look at app versions --> v.2 should show as "live"

## Lab: Docker container + Beanstalk
- AWS console : compute : Elastic Beanstalk
- create a web app
  + name
  + platform = 1 Docker
  + upload code (zip file with Dockerfile & `app.py`)
  + will give you a unique version label
  + --> upload --> create app --> wait
- back in Elastic Beanstalk console, see that the app is healthy & online
  - --> see URL near top --> open the URL in a new tab --> see your new website running on Docker containers in Elastic Beanstalk
- upgrade to V2
  - "upload & deploy" --> upload V2 of zip file --> wait --> see V2 online after a few mins
- revert to previous version
  - "upload & deploy" --> see info banner: "to deploy a previous version, go to 'app versions' page"
  - --> select previous version
  - --> "actions" dropdown
  - --> click "deploy"
- terminate
  - go to "environments" left tab --> "actions" dropdown --> click "delete app" --> deletes entire Elastic Beanstalk environment for you