# Lambda

## Quick facts
- Lambda is a _serverless_, __compute service__
  - serverless includes lambda, S2, API Gateway, Dynamo DB, etc.
- continuous scaling in parallel -- scales out (not up!) automatically
- languages: Node, Java, Python, C#, Go
- pros: no servers, continuous scaling (in parallel), super cheap

## Details
- idea of abstraction, with Lambda being super abstracted
  - __data centers__ contain __hardware__ running __assembly code__
  - humans prefer __high-level languages__ -- __operating systems (OS)__ help communicate between high-level languages & assembly
- scaling out
  - by default, 1000 Lambdas per region per account at any time (see "concurrent executions" section)
  - one Lambda can trigger any # of other Lambda functions (e.g. 1 function triggers 3 others)
  - architecture can get very complicated; should debug with AWS X-ray
- pricing:
  - first million requests per month = free
  - after that = $0.20 / request
  - also based on duration: $0.00001667 / GB-sec

## Versioning
- 1+ versions of a function
- can use diff variations in beta, gamma, prod
- each function version has a unique ARN (Amazon Resource Name); this is immutable
- only `$LATEST` version can be updated
- you can refer to a function using its ARN --> e.g. `arn:aws:lambda:blah:$LATEST`
- using an __alias__:
  - create lambda with `$LATEST` version → publish as v. 1 → create alias "prod" → _prod_ can now invoke v. 1
  - you can then update to v. 2 → point _prod_ to v. 2
  - you can then roll back _prod_ to v.1 if needed
  - you can also split traffic by specific amounts using aliases to diff. versions (but _not_ `$LATEST`)

## Concurrent Executions Limit
- if you have too many Lambda functions running in the same region, and you suddenly start seeing new Lambda invocation requests get rejected, you may have hit your limit
- __concurrent executions__
  - by default, max 1000 Lambdas per region at any time
  - after hitting limit, you'll get `TooManyRequestsException` or HTTP status code 429
  - (?) is a safety feature to limit costs
  - --> solution: request increases via AWS support center
- __reserved concurrency__
  - guarantees a set # of executions will always be available for critical functions (e.g. you've reserved 500 Lambdas for a critical function)
  - can act as a limit in the same way as concurrent executions

-----

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
