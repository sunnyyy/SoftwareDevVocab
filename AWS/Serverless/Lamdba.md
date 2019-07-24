# Serverless 101

___

# Lambda

## Layers of abstraction
- __data centers__ contain __hardware__ running __assembly code__
- humans prefer __high-level languages__ -- __operating systems (OS)__ help communicate between high-level languages & assembly
- end users run an __application layer__, which may call the AWS __API Gateway__, which may call services running in AWS __Lambda__

## Lambda details
- Lambda is a _serverless_, __compute service__
  * serverless includes lambda, S2, API Gateway, Dynamo DB, etc.
- continuous scaling in parallel -- scales out (not up!) automatically
  * can trigger any # of other lambda functions (e.g. 1 function can trigger 3 others)
  * architecture can get very complicated; should debug with AWS X-ray
- languages: Node, Java, Python, C#, Go
- pricing:
  * first million requests per month = free
  * after that = $0.20 / request
  * also based on duration: $0.00001667 / GB-sec
- pros: no servers, continuous scaling (in parallel), super cheap

## Versioning
- 1+ versions of a function
- can use diff variations in beta, gamma, prod
- each function version has a unique ARN (Amazon Resource Name); immutable
- only `$LATEST` version can be updated
- you can refer to a function using its ARN
  * qualified ARN: contains version suffix -- ex: `arn:aws:lambda:blah:$LATEST`
  * unqualified ARN: lacks version -- ex: `arn:aws:lambda:blah`
- using an __alias__:
  * create lambda with `$LATEST` version → publish as v. 1 → create alias "prod" → _prod_ can now invoke v. 1
  * you can then update to v. 2 → point _prod_ to v. 2
  * you can then roll back _prod_ to v.1 if needed
  * you can also split traffic by specific amounts using aliases to diff. versions (but _not_ `$LATEST`)

## Time out: What's a (regular, non-AWS) lambda function? I totally forgot!

regular function in Python:
```Python
def add(x, y):
	return x + y

add(2, 3)			# output = 5
```

lambda function in Python:
```Python
add = lambda x, y : x + y

print add(2, 3)		# output = 5

type(add)			# output = function
```

mapping:
```Python
map(function, iterable1, iterable1, ...)

map(lambda x: x*2, [1,2,3,4])			# output = [2,4,6,8]
```

filtering (aka finding matches):
```Python
filter(function, iterable)

filter(lambda x: x%2 == 0, [5,6,7,8])	# output = [false, true, false, true]
```

___


# API Gateway

## What's an API?
- restaurant analogy
  * customer = user
  * menu = GUI
  * waiter = API
  * chef = backend
- types
  * old: __SOAP__ (simple object access protocol) -- uses XML
  * modern: __REST__ (representational state transfer) -- uses JSON

## AWS's API Gateway can...
- expose HTTPS endpoints to define a RESTful API
- serverless-ly connect to Lambda & DynamoDB
- send API endpoints to diff. targets
- run efficiently / low-cost
- scale effortlessly
- track / control usage by API key
- can throttle requests to limit attacks
- connect to CloudWatch to log/monitor stuff

## How to configure?
- define API container
- define resources / nested resources (URL paths)
- for each resource:
  * select supported HTTP methods
  * set security
  * choose target (EC2, Lambda, etc.)
  * set request / response transformations
- deploy API to a stage (beta, prod, etc.)
  * by default uses API Gateway domain but can customize
  * supports AWS Certification Manager (free SSL / TLS certs)

## API caching
- cache response from API call
- reduces calls & improves latency
- specify Time-To-Live (TTL) period in seconds

## Same-origin policy
- a web browser allows scripts in webpage A to access data in webpage B only if both have the same origin
- can prevent cross-site scripting (XSS) attacks
- enforced by browsers
- ignored by tools like `curl` & PostMan

## Cross-origin resource-sharing (CORS)
- the server (not the client code in browser) can relax the same-origin policy rule
- allows restricted resources (e.g. fonts) to be requested from an outside domain
- to implement:
  * browser calls via `GET`, `PUT`, `POST` HTTP calls
  * server says "these `___` domains are approved to `GET` this URL"
  * if not enabled, error: "origin policy cannot be read"

## Exam tips
- know at high level what an API is
- can cache responses; can throttle
- low cost; scales
- can log with Cloudwatch
- if you use multiple domains, enable CORS
- CORS is enforced by the client **`(?)`**

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