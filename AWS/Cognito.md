# Cognito

## Quick facts
- __Web Identity Federation__
  * lets you give access to AWS resources after they're authenticated with another web ID provider (e.g. FB, Google, Amazon)
  * after stressful (e.g. FB) authentication, user receives an auth code from the web ID provider, which they can trade for temp AWS login
- __AWS Cognito__
  * provides web identity federation with sign up, sign in, and guest access to your app
  * brokers info between (e.g. FB) and your app
  * syncs user data across multiple devices
  * is the AWS-recommended approach to web ID federation, especially for mobile devices

## Details
- acts as an __identity broker__ between your app & web ID providers
  * after (e.g. FB) login, provides temp credentials that map to an IAM role allowing access to the required resources in your app
  * no need for extra code on your part
  * no need to store AWS credentials locally on device
- syncs user data across devices
  * Cognito tracks the association between user ID & their many devices
  * uses __push synchronization__ (specifically, _AWS's SNS_) to send a silent push notification to all the devices associated with a given user ID whenever data stored in the cloud changes
  * these push notifications then tell your devices to sync your data, to provide a seamless UX
- highly recommended for mobile devices across AWS (???)

### example: shopping app
- S3 for shop data
- DynamoDB for customer data
- customer logs into FB --> AWS Congnito gives them temp credentials
- temp credentials --> customer logs into your app

## Cognito : User Pools
- __user pool__
  * user directives (???) used to manage sign up / sign in for mobile / web apps
  * users can sign in directly to user pool or indirectly via an ID provider (e.g. FB)
  * successful authentication generates a number of JSON web tokens ("JWT")
- __identity pools__
  * allow you to create unique Identities for users & authenticate them with ID providers
  * with an Identity, you can obtain temp, limited-privilege AWS credentials to access other AWS services
- example:
  * __user__ -->
    - <--> __user pool__ <-- _JWT tokens_ --> __FB__
    - <-- _AWS credentials_ --> __identity pool__
    - <--> __AWS resources__

