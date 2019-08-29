# SAM = serverless application model

## Quick facts
- extension to CloudFormation to define serverless apps
- simplified syntax for defining serverless resources (e.g. APIs, Lambdas, DynamoDB tables, etc.)
- use SAM CLI to package deployment code, upload to S3, or deploy serverless app

## Commands
```
sam package ?		# takes template
			?		# outputs SAM-compatible template
			?		# uploads it to the specified S3 bucket

sam deploy 	?		# takes that SAM template
			?		# deploys it to your CloudFormation stack
			?		# creates an IAM role on your behalf so it can execute
```

-----
# SAM Labs

## Lab