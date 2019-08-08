# Elastic Beanstalk

## Quick facts
- deploy / scale web apps onto server platforms
  - languages supported: Java, .NET, PHP, Node.js, Python, Ruby, Go, Docker
  - server platforms supported: Apache Tomcat, Nginx, Passenger, IIS
- provisioning service
- when you upload code to Beanstalk, it'll handle:
  - deployment
  - capacity provisioning
  - load balancing
  - auto-scaling
  - app health
- you can retain full control of the underlying AWS resources powering your app
- you only pay for the AWS resources required to store / run your apps (e.g. EC2 instances & S3 buckets)

## Details
- fastest / simplest way to deploy your app in AWS
  - devs can focus on code instead of underlying infrastructure
- auto-scales your app up/down
- you can select the EC2 instance

## Customization
- you can customize a Beanstalk environment using config files
  - define packages to install
  - create Linux users / groups
  - run shell commands
  - specify services to enable/configure your load balancer
  - etc.
- config files details:
  - written in YAML or JSON
  - files must have `.config` extension
  - files must be saved in a directory called `.ebextensions` at the top-level directory of your app code bundle

___

# RDS (Relational Database Services) + Beanstalk

Elastic Beanstalk supports 2 ways of integrating RDS
1. launch RDS instance from within Beanstalk console
  - quick / easy to add your database & get started
  - when you terminate the Beanstalk environment, the database will also be terminated
  - suitable for Dev / Test environments but _NOT_ for Prod!
2. launch RDS outside Beanstalk, directly from RDS console
  - more flexibility; wider choice of database types
  - allows connection from multiple Beanstalk environments to the same RDS database
  - you can tear down the app stack without impacting the database
  - suitable for Prod environments
  - 2 extra config steps:
    1. you must add an additional security group to your Beanstalk environment's auto-scaling group
    2. you'll need to provide connection info to your app servers (endpoint, password)