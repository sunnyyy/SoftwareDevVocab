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