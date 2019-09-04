# Systems Manager Parameter Store

## Quick facts
- you can store confidential info such as passwords, DB connection strings, license keys, etc. in the SSM parameter store
- values stored as as plain text or encrypt
- these values are then referenced by their names
- you can then ...
  - organize parameters into hierarchies
  - create system manager parameters (?)
  - tag parameters
  - work with parameter versions
- you can use this with:
  - EC2
  - CloudFormation
  - Lambda
  - EC2 run command
  - etc.

-----

# Labs

## Lab: adding a value into the SSM parameter store
- console --> EC2 --> "Parameter Store" in left tab
- create parameter --> secure string (encrypted with KMS) --> "create parameter" --> you can see it listed but not its raw value (unless you have permission)