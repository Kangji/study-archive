# GoF Design Patterns

- Creational: object instantiation
    - Factory Method
    - Abstract Factory
    - Builder
    - Prototype
    - Singleton
- Structural: object composition
    - Adapter
    - Bridge
    - Composite
    - Decorator
    - Facade
    - Proxy
    - ~~Flyweight~~
- Behavioral: assignment of responsibilities between objects
    - Chain of Responsibility
    - ~~Command~~
    - Iterator
    - Mediator
    - ~~Memento~~
    - Observer
    - State
    - Strategy
    - ~~Template Method~~
    - Visitor

## Factory Patterns

- Factory Method
    - Abstract class with factory method which returns abstract class (product)
    - Concrete classes implement factory method which returns concrete class (product)
- Abstract Factory
    - Group of factory methods
    - Two orthogonal domains: factory and product

## Builder

- Encapsulates the dynamic construction
- Director holds a predefined combination of builder options

## Prototype

- `Clone` trait

## Singleton

- Ensures a single instance
- Private constructor
- `getInstance()`
    - Multithreading Issue ⇒ Synchronize
- `synchronized`
    - Performance Issue ⇒ Double Check to avoid contention after created
    - OOO Execution / Cache Coherence ⇒ volatile
- Early initialization
    - Early initialized static field
- Static holder singleton
    - Use private static inner class with instance as static field
    - Private static inner class is initialized when first accessed (lazy)

## Adapter

- Convert an interface into another interface as clients expect
- Composition of adaptee / implement target interface

## Bridge

- Split the large class into two parts
- Composition of parts (interface)

## Composite

- Tree structure: Leaf Component or Composite Component (Group of Component) both implements Component interface

## Decorator

- **Dynamically** attaches additional features in **runtime**

## Facade

- A **god** object which provides a unified interface to a set of interface subsystems
- Defines higher level interface

## Proxy

- Access-controls to the original object
- Class diagram is similar with decorator, but the difference is access control

### Chain of Responsibility

- Chain of handlers, each handles independent operation and pass input to next
- Chain is mutable in runtime
- Class diagram is similar with decorator & proxy

## Iterator

- Iterates over the data structure without revealing it’s structure

## Mediator

- All interaction between objects are managed by central mediator in order to loose the (many-to-many) dependencies.
- Each object now only depends on mediator object.

## Observer

- Pub/sub structure
- Subject(Publisher) invokes the method of Observer(Subscriber)
- Prevents polling

## State

- State Machine
- Delegates code which varies based on the state, including state transition code

## Strategy

- Similar to state but there is no transition
- Class that represents concrete algorithm
- Unlike state, the object changes the strategy itself

## Visitor

- Visitor & Element Pair
- Visitor implements a single operation of all concrete elements
- Visitor is extendable but Element is not
