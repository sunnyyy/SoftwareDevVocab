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
