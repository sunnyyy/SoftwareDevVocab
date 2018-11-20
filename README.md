# Software Development Vocab

This is an ever-expanding list of vocab + definitions that I find useful as a software development engineer.

## Agile

- __Agile software development__ = a set of values and principles for software development; involves self-organizing cross-functional teams
- __daily scrum / stand up__ = daily update @ 10:45 now 11:45; give teammates 30-sec brief on what you worked on yesterday + what you'll work on today
- __P0 = Priority #0__ ; comes before everything else
- __sprint = 2-week periods, Wed-Tues
  - sprint planning estimates (for 1/2/4/8/16; may also use Fibonacci):
    - 1 = already know how to fix the bug
    - 2 = minor bug, requires some research
    - 4 = bug would take 1 week
    - 8 = bug would take entire sprint, aka 2 weeks
  - __sprint board__
    - board with columns for "to-do", "in-progress", "code review", and "done", used to track work done in a given sprint
    - many companies use a tool called [Jira](https://en.wikipedia.org/wiki/Jira_%28software%29)
- __scrum master__ = facilitator for an agile development team
- __velocity__ = measure of the amount of work a team can tackle during a single sprint

## Job Titles

- __PM = Product Manager__ or __Program Manager__ ; it varies by company and idk if Product > Program or vice-versa
- __QAE = Quality Assurance Engineer__ ; at Amazon, involved with manual testing
- __SDE = Software Development Engineer__ (Amazon)
  - Levels: SDE I, SDE II, SDE III, Principal Engineer, Senior Principal Engineer, Distinguishe engineer
- __SDET = Software Development Engineer in Test__ ; at Amazon, writes automation tests
- __SDM = Software Development Manager__ (Amazon)
- __SWE = Software Engineer__ (Google)
  - Levels: SWE I, SWE II, SWE III, Senior Engineer, Staff Engineer, Senior Staff Engineer, Principal Engineer, Distinguished Engineer, Google Fellow, Senior Google Fellow

## Software Tools

- __Dagger__ = dependency injection tool ; [website](https://google.github.io/dagger/)
- __Gerrit__ = code review tool ; [wiki](https://en.wikipedia.org/wiki/Gerrit_%28software%29)
- __Jenkins__ = automation tool to generate builds ; [wiki](https://en.wikipedia.org/wiki/Jenkins_%28software%29)
- __Jira__ = sprint board ; [wiki](https://en.wikipedia.org/wiki/Jira_%28software%29)

## Software Development: General

- __build artifacts__ = files produced by a build
  - can include distribution packages, reports, log files, etc.
- __CR = code review__
- __dependency injection__ = one object supplies the dependencies of another object; [wiki](https://en.wikipedia.org/wiki/Dependency_injection)
  - ex: for Android dev, `@inject`
- __POJO = plain old Java object__
- __Runbook__ = instructions for what to do when you're on-call
- __source code__ = any collection of code + comments written using a human-readable programming language (vs. binary machine code, which is readable only by a computer)
- __SSO = single sign-on__
- __Tickets__ = customer issues

## Software Development: Mobile

- Android development
  - __Android Studio__ = IDE for Android development ; [website](https://developer.android.com/studio/)
  - __Gradle__ = automation tool to generate builds ; [wiki](https://en.wikipedia.org/wiki/Gradle)
  - __Kotlin__ -- [wiki](https://en.wikipedia.org/wiki/Kotlin_%28programming_language%29)
  	- a programming language that can be compiled to many languages
  	- known for its aggressive type inference, aka will have fewer null pointer exceptions relative to Java
  - __ProGuard__ = open-source command-line tool that shrinks/optimizes/obfuscates Java bytecode
- iOS development
  - __XCode__ = IDE for iOS & MacOS development ; [website](https://developer.apple.com/xcode/)

## Software Development: Services

- __chaos engineering__ = identifying failures before they become outages ; [learn more](https://www.gremlin.com/community/tutorials/chaos-engineering-the-history-principles-and-practice/)
  - ex: Amazon's [GameDays](https://www.gremlin.com/community/tutorials/how-to-run-a-gameday/) = testing on a service / system to see how it responds to stress, in prep for big shopping days like Black Friday, Xmas
- __continuous deployment__ = updates auto tested & then deployed if tests succeed
- __DAO = data access object__ ; [wiki](https://en.wikipedia.org/wiki/Data_access_object)
- __SOA = service-oriented architecture__
  - systems are built up from collections of small services (e.g. a single web service)
  - small changes to building blocks keep systems up & running; very scalable



