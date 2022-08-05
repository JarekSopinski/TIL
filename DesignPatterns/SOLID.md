## SOLID Design Principles

### Single Responsibility Principle

Each class should have exactly one responsibilty.
Connected to this is Separation of Concerns - splitting complicated algorithm into several parts that are related, making entire system easier to read, manage and refactor.

### Open-Closed Principle

Classes should be open for extension, by closed for modification. Modification: i.e. adding new methods. Extension means usually inheritance. Modifing finished, tested classes is not a good idea. A better approach is to use inheritance, i.e. specification classes.

### Liskov Substitution Principle

You should be able to substitute a base type for a subtype. If we have a method that takes base class, it should also equally be able to take derived (inherited) class, without breaking functionality.

### Interface Segregation Principle

You have to segregate (split up) interfaces into different parts so that people don't implement more that they need. I.e. don't force implementation of empty methods. Don't put too much into an interfece, split it into separate interfeces.

### Dependency Inversion Principle

Definies relationship that you should have between low level modules and high level modules. Low-level modules are concerned with low level things such as storage. High-level modules should not directly depend on low-level modules. They should instead depend on abstractions (abstract classes or interfeces).