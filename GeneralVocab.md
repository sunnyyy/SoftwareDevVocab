# General Vocab

- __build artifacts__ = files produced by a build
  - can include distribution packages, reports, log files, etc.
- __Config file__ = plaintext file that specifies parameters & initial settings for some programs
  - ex: `.git/config` at the root of a git repo
  - ex: `.ssh/config` for setting up SSH keys
- __CR = code review__
- [__dependency injection__](https://en.wikipedia.org/wiki/Dependency_injection) = one object supplies the dependencies of another object
  - ex: for Android dev, `@inject`
- __POJO = plain old Java object__
- __Runbook__ = instructions for what to do when you're on-call
- __source code__ = any collection of code + comments written using a human-readable programming language (vs. binary machine code, which is readable only by a computer)
- __SSO = single sign-on__
- __tickets__ = customer issues
- __version control__ = software that manages code changes; changes are called "revision"; ex: Git
- __version set__ = snapshot of a _set_ of dependencies in a package

-----

# Software Tools

- [__Dagger__](https://google.github.io/dagger/) = dependency injection tool ⟶ ([website])
- [__Gerrit__](https://en.wikipedia.org/wiki/Gerrit_%28software%29) = code review tool ⟶ ([wiki])
- __Jenkins__ = automation tool to generate builds
- [__Jira__](https://en.wikipedia.org/wiki/Jira_%28software%29) = sprint board

-----

# Project Management Methodologies

- __Waterfall__
  - very linear dev process
  - project requirements need to be clear up front
  - requirement gathering ⟶ documentation ⟶ system design ⟶ implementation ⟶ testing ⟶ delivery / deployment ⟶ maintenance 
- __Agile__
  - adaptive, simultaneous workflows
  - break projects into smaller, iterative periods
  - products benefit from user testing and regular revision
- __Kanban__
  - a type of Agile process
  - __Kanban board__
      - board with columns for "to-do", "in-progress", "code review", and "done", used to track work done in a given sprint
      - many companies use a tool called [Jira](https://en.wikipedia.org/wiki/Jira_%28software%29)
  - makes problems like bottlenecks highly visible
- __Scrum__
  - a type of Agile process
  - uses two-week __sprints__ to get work done
  - __daily scrum / stand up__ = daily update to teammates about work done yesterday / planned work for today
  - during sprint planning, the team creates a sprint backlog
  - sprint planning estimates (for 1/2/4/8/16; may also use Fibonacci):
    - 1 = already know how to fix the bug
    - 2 = minor bug, requires some research
    - 4 = bug would take 1 week
    - 8 = bug would take entire sprint, aka 2 weeks
  - __scrum master__ = kind of like a project manager
  - __velocity__ = measure of the amount of work a team can tackle during a single sprint
- Misc. vocab
  - __P0 = Priority #0__ = minimum required for release