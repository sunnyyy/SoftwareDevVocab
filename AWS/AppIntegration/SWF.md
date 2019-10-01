# Simple Workflow Service (SWF)

## Quick facts
- web service that makes it easy to coordinate work across distributed app components
- enables apps for a range of use cases to be designed as a coordination of tasks
- __task__ = a step that is performed by ...
  - executable code
  - service calls
  - human actions
  - scripts
- example use cases:
  - media processing
  - web app backends
  - business process flows
    - warehouse coordination
  - analytics pipelines

## SWF vs. SQS

|                      | SWF                        | SQS              |
|----------------------|----------------------------|------------------|
| retention period     | 14 days                    | 1 year           |
| API                  | task-oriented              | message-oriented |
| message duplication? | none; task only handled 1x | possibly; you must ensure message is processed only 1x |
| task tracking        | handled for you            | must implement your own app-level tracking (esp. if you have multiple queues) |

## SWF actors
- __starters__ = app that can initialize a workflow
  - e.g. online order kicks off app action
- __deciders__ = control flow of activity tasks in a workflow execution
  - e.g. if something finishes or fails, what to do next?
- __activity worker__ = carry out task