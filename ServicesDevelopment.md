# Services Development

- __chaos engineering__ = identifying failures before they become outages ⟶ ([learn more](https://www.gremlin.com/community/tutorials/chaos-engineering-the-history-principles-and-practice/))
  - ex: Amazon's [GameDays](https://www.gremlin.com/community/tutorials/how-to-run-a-gameday/) = testing on a service / system to see how it responds to stress, in prep for big shopping days like Black Friday, Xmas
- __continuous deployment__ = updates auto tested & then deployed if tests succeed
- __environment stage__ = a collection of machines called hosts
  - no need to worry about the health of individual hosts; can replace unhealthy hosts with new hosts as needed
  - ex: _alpha, beta, gamma, prod_ are each a stage
  - each stage lets you test a larger part of the system
  - __pipeline__ ([wiki](https://en.wikipedia.org/wiki/Pipeline_%28software%29))
    - a chain of processes such that each element is the input of the next
    - ex: alpha ⟶ beta ⟶ gamma ⟶ prod
- __DAO = data access object__ ([wiki](https://en.wikipedia.org/wiki/Data_access_object))
- __SOA = service-oriented architecture__
  - systems are built up from collections of small services (e.g. a single web service)
  - small changes to building blocks keep systems up & running; very scalable
