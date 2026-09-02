# OOP Concepts

- Abstraction: hide complexity, expose only essential details through interfaces or abstract classes.
- Encapsulation: keep fields private and expose access through methods so updates stay in one place.
- Inheritance
- Polymorphism: method overloading and method overriding.
- Cohesion: how related the functions within a single module are.
- Coupling: interdependencies between modules.

High cohesion, low coupling.

# SOLID Principles

- Single Responsibility: one reason to change.
- Open-Closed Principle: open for extension, closed for modification.
- Liskov Substitution Principle: subclasses should be substitutable for their base types.
- Interface Segregation: prefer multiple smaller interfaces over one large interface.
- Dependency Inversion: depend on abstractions, not concrete implementations.

Rule of thumb: interface over implementation, object decomposition over inheritance.

# Design Patterns

Creational, Structural, Behavioral.

## Creational

- Factory Method
- Abstract Factory: factory of factories.
- Builder: build complex objects using smaller steps.
- Prototype: cloning.
- Singleton: single instance.

## Structural

- Adapter: compatible wrapper for incompatible interfaces.
- Bridge: decouple abstraction from implementation.
- Composite: treat groups of objects uniformly.
- Decorator: add behavior without changing the original class.
- Facade: hide complexity behind a simple interface.
- Flyweight: reduce object creation by reusing shared state.
- Proxy: stand-in object that controls access to another object.

## Behavioral

- Chain of Responsibility
- Command: represent actions as objects.
- Interpreter: language grammar, SQL parsing.
- Iterator: next-based traversal.
- Mediator: communication between classes through a central object.
- Memento: restore a previous state.
- Observer: register and notify.
- State: behavior changes with context.
- Null Object: do-nothing object instead of returning null.
- Strategy: swap algorithms at runtime.
- Template Method: define the flow in an abstract class.
- Visitor: add operations without changing the visited elements.

# MVC
