# Serverless: other AWS services

___

# Step Functions

- tool that allows you to visualize / test serverless apps
- provides GUI to arrange / visualize components of your app as a series of steps
- auto-triggers / tracks each step; retries when there are errors, so your app executes in order / as expected
- logs state of each step; easier to diagnose / debug issues

___

# X-ray

- tool that collects data about requests to your app
- provides tools to view, filter, gain insight into that data to identify issues & opportunities for optimization
- detailed info about request / response + any calls your app makes to downstream AWS resources, microservices, DBs, web APIs
- X-ray architecture: SDK → daemon → API → console

## The X-ray SDK has...
- interceptors to add to your code to trace incoming HTTP requests
- client handlers to instrument AWS SDK clients, to call other AWS services
- an HTTP client to instrument calls to other HTTP web services

## It integrates with...
- Elastic Load Balancing
- Lambda
- API Gateway
- EC2
- Elastic Beanstalk