# Design Patterns

## Purpose
- solve common problems
- concise vocab to discuss design problems & solutions

## Creational patterns

### Singleton
- ensures at most 1 instance of a class exists at any given time
- acts as gatekeeper for shared resources
- acts as central communications hub
- all methods accessed via singleton
- __dynamic binding__ = the class used to create the singleton can be determined at runtime, not necessarily at compile time 
- examples:
  - java.lang.runtime class
  - loggers
- pros:
  - better than a set of static methods --> can still take advantage of interfaces & inheritance
- cons:
  - methods must be sync'ed every time env is multithreaded --> slower
  - may slow app startup time
  - may hold onto resources too long

### Builder
- creates objs in a stepwise manner without caring about how the objects are constructed
- object initiation is clearer
- init params can easily be added / removed

### Factory method
- base class defines a method that can be overriden in a subclass
- enables subclass to determine how a new obj is made

### Abstract factory
- a set of factory classes inherit from an abstract class
- app refers to factory implementation exclusively via an abstract class, not a concrete class (??)
- factory selection can be deferred until runtime or even changed midway during exec (??)
- do not use abstract factory if:
  - factories are different from each other (??)
  - there will only be 1 factory class

## Behavioral patterns

### Iterator
- traverse through all elements of a data structure without knowing / caring how they're stored / implemented
- simple iterators = unidirectional travel + no changes to underlying structure
- complex iterators can allow for bidirectional travel and/or changes to underlying structure

### Observer (aka "pub-sub" model)
- observer registers with subject using common interface for update notifications
- subject notifies observers when its state changes
- examples:
  - MVC -- changes to model (underlying data) causes views (UI) to redraw themselves
cons:
  - can lead to slower performance due to constant checking

## Structural patterns

### Decorator (aka "wrapper") (???)
- 4 kinds
  - component
  - concrete component
  - decorator
  - concrete decorator
