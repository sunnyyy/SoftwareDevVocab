# IAM = Identity Management Access

## Quick facts
- vocab
  - users = people
  - group = collections of users
  - role = set of permissions
  - policy = doc that defines 1+ permissions
- 

## Details
- centralized control of AWS account
- shared access
- granular permissions (diff levels of permissions)
- identity federation (external login, e.g. via FB)
- should always set up MFA/2FA on root account
- temp access for users/devices/services
- set up PW rotation policy (e.g. PW expires in 90 days)
- integrates w/ many diff AWS services
- supports PCI DSS compliance ($ card)
- is universal within AWS --> no regions
- root account = account that signed up with AWS
- when new users are first created ...
  - they have no permissions --> permissions must be assigned
  - are assigned an access key ID + secret access key --> only viewable 1x --> if lost, must regenerate
- you can create + customize your own password rotation policies

## IAM policies
- 3 types: managed, customer-managed, and inline

### Managed policy
- created / admin'ed by AWS
- AWS provides managed policies for common use cases based on job function
  * ex: AmazonDynamoDBFullAccess
  * ex: AmazonEC2ReadOnlyAccess
- these AWS policies allow you to assign permissions to your users / groups / roles without having to write the policy from scratch
- a single managed policy can be attached to multiple users / groups / roles within the same AWS account & across different accounts
- written in JSON, but _cannot_ be edited / changed

### Customer-managed policy
- standalone policy that you create / admin within your own AWS account
- you can attah it to multiple users / groups / roles but only within your own account
- to create a customer-managed policy, you can copy an existing AWS managed policy & customize it
- recommended for if AWS managed policies don't fit your use case
- written in JSON, _can_ be edited

### Inline policy
- IAM policy embedded within the user / group / role
- strict 1:1 relationship between entity & policy (???)
- when you delete that user / group / role, the proxy will also be deleted
- AWS generally recommends managed > inline
- written in JSON, _can_ be edited (???????????)
- inline policies useful when you're creating a policy that must never (accidentally) be attached to the wrong user / group / role
