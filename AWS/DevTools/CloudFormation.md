# CloudFormation

## Quick facts
- allows you to manage, configure, and provision your AWS infrastructure as code
- resources are defined in the CloudFormation template
- CloudFormation interprets the template & makes the API calls to create the resources you defined

## Details
- the infrastructure is provisioned consistently, with few mistakes
- less time & effort than manual configuration
- you can version control & peer review templates
- free to use; charged for resources created
- can be used to:
  * manage updates
  * manage dependencies
  * rollback
  * delete the entire stack

## Template format
- YAML or JSON
- describes the endstate of the infrastructure you're provisioning or changing
- after creating the template, you upload it to CloudFormation via S3
- CloudFormation reads the template & makes API calls on your behalf
- the resulting resources are called a __"Stack"__
- main sections of the template
  * __parameters__ -- allows you to imput custom values, e.g. env type
  * __conditions__ -- e.g. provision resources based on env
  * __resources__ (mandatory) -- the AWS resources you want to create, e.g. EC2 instances, DynamoDB table, etc.
  * __mappings__ -- e.g. region : AMI
  * __transform__ -- used to reference additional code in S3; allows for code reuse
    - e.g. Lambda code
    - e.g. recyclable snippets of the CloudFormation template

## EXAMPLE TEMPLATE

## Nested stacks
- nested stacks allow reuse of CloudFormation code for common use cases
  * e.g. standard config for a LB, web server, app server, etc.
- instead of copying code each time, you create a standard template for each common use cases & reference it from within another CloudFormation template
  * ⟶ becomes a template within a template
- to do this:
  * create a CloudFormation template
  * ⟶ store it in S3
  * ⟶ reference it in the __*Resources*__ section of any (future) CloudFormation template using the __*Stack*__ resource type

-----
# CloudFormation Labs

## Lab
- managment tools --> CloudFormation --> create a new stack --> select template (sample or upload) --> one previously uploaded to S3 (?)