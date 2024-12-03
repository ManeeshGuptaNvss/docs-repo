# SOLID Principles

## S - Single Responsibility Principle

A class should have only one reason to change.


Martin defines a responsibility as a *reason to change*, and concludes that a class or module should have one, and only one, reason to be changed (e.g. rewritten).

As an example, consider a module that compiles and prints a report. Imagine such a module can be changed for two reasons. First, the content of the report could change. Second, the format of the report could change. These two things change for different causes. The single responsibility principle says that these two aspects of the problem are really two separate [responsibilities](https://en.wikipedia.org/wiki/Interface_(computing)), and should, therefore, be in separate classes or modules. It would be a bad design to [couple](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) two things that change for different reasons at different times.

The reason it is important to keep a class focused on a single concern is that it makes the class more robust. Continuing with the foregoing example, if there is a change to the report compilation process, there is a greater danger that the printing code will break if it is part of the same class.
[Taken From](https://en.wikipedia.org/wiki/Single-responsibility_principle#:~:text=The%20reason%20it%20is%20important,part%20of%20the%20same%20class).

---

## O - Open Closed Principle

Open for extension closed for modification. A class which is throughly tested and live in production. Then it is not good to modify the class. Instead one can extend the class and do necessary modification.

> A class is closed, since it may be compiled, stored in a library, [baselined](https://en.wikipedia.org/wiki/Baseline_(configuration_management)), and used by client classes. But it is also open, since any new class may use it as parent, adding new features. When a descendant class is defined, there is no need to change the original or to disturb its clients.
> 

[https://en.wikipedia.org/wiki/Open–closed_principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)

## L - Liskov Substitution Principle

When we substitute the parent class objects with the child class objects then the program should work without breaking.

1. **Definition**: The Liskov Substitution Principle states that objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.
2. **Key Idea**: Subtypes must be able to fulfill the contract of their base types, ensuring that derived classes can be used interchangeably with their base classes without introducing errors.
3. **Purpose**: To maintain reliable and predictable behavior in an object-oriented system by ensuring that subclass objects can substitute their superclass objects seamlessly.

```mermaid
classDiagram
    class Bird {
        +fly() : void
    }

    Bird <|-- Penguin

    class Penguin {
        +fly() : void
    }

```

```java linenums="1"
// Bird.java
public class Bird {
    public void fly() {
        System.out.println("Flying");
    }
}

// Penguin.java
public class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Penguins can't fly");
    }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        Bird bird = new Bird();
        Bird penguin = new Penguin();

        bird.fly();       // Works fine
        penguin.fly();    // Throws exception, violating LSP
    }
}

```

---

```mermaid
classDiagram
    class Bird {
        +eat() : void
    }

    class Flyable {
        <<interface>>
        +fly() : void
    }

    Bird <|-- Sparrow
    Bird <|-- Penguin
    Sparrow ..|> Flyable

    class Sparrow {
        +eat() : void
        +fly() : void
    }

    class Penguin {
        +eat() : void
    }

```

```java linenums="1"
// Bird.java
public abstract class Bird {
    public abstract void eat();
}

// Flyable.java
public interface Flyable {
    void fly();
}

// Sparrow.java
public class Sparrow extends Bird implements Flyable {
    @Override
    public void eat() {
        System.out.println("Sparrow is eating");
    }

    @Override
    public void fly() {
        System.out.println("Sparrow is flying");
    }
}

// Penguin.java
public class Penguin extends Bird {
    @Override
    public void eat() {
        System.out.println("Penguin is eating");
    }

    // Penguins do not implement Flyable
}

// Main.java
public class Main {
    public static void main(String[] args) {
        Bird sparrow = new Sparrow();
        Bird penguin = new Penguin();

        sparrow.eat();     // Works fine
        ((Flyable) sparrow).fly();  // Works fine

        penguin.eat();     // Works fine
        // penguin cannot fly, no cast to Flyable, adhering to LSP
    }
}

```

## I - Interface Segregation Principle

[Reference](https://www.baeldung.com/java-interface-segregation#doing_it_correct)

This principle was first defined by Robert C. Martin as: “**Clients should not be forced to depend upon interfaces that they do not use**“.

The goal of this principle is to **reduce the side effects of using larger interfaces by breaking application interfaces into smaller ones**. It’s similar to the [Single Responsibility Principle](https://www.baeldung.com/java-single-responsibility-principle), where each class or interface serves a single purpose.

Precise application design and correct abstraction is the key behind the Interface Segregation Principle. **Though it’ll take more time and effort in the design phase of an application and might increase the code complexity, in the end, we get a flexible code.**

=== "Before"

  ```mermaid
  classDiagram
      class IPayment {
          +void initiatePayments()
          +Object status()
          +List<Object> getPayments()
          +void intiateLoanSettlement()
          +void initiateRePayment()
      }
      class LoanPayment {
          +void initiatePayments()
          +Object status()
          +List<Object> getPayments()
          +void intiateLoanSettlement()
          +void initiateRePayment()
      }
      class BankPayment {
          +void initiatePayments()
          +Object status()
          +List<Object> getPayments()
          +void intiateLoanSettlement() UnsupportedOperationException
          +void initiateRePayment() UnsupportedOperationException
      }

      IPayment <|-- BankPayment : implements
      IPayment <|-- LoanPayment : implements

  ```

=== "After"

  ```mermaid
  classDiagram

      class IPayment {
          +Object status()
          +List<Object> getPayments()
      }
      class IBank {
          +void initiatePayments()
      }
      class ILoan {
          +void initiateLoanSettlement()
          +void initiateRePayment()
      }
      class BankPayment {
          +void initiatePayments()
          +Object status()
          +List<Object> getPayments()
      }
      class LoanPayment {
          +void initiateLoanSettlement()
          +void initiateRePayment()
          +Object status()
          +List<Object> getPayments()
      }
      
      IPayment <|.. IBank : extends
      IPayment <|.. ILoan : extends
      IBank <|-- BankPayment  : implements
      ILoan <|-- LoanPayment  : implements
  ```

## D - Dependency Inversion Principle

Class should depend on interfaces rather than concrete classes.

**Why Is the Dependency Inversion Principle Important**

When we write code for applications, we might split our logic into multiple modules. Nonetheless, this will result in a code with dependencies. One motivation behind DIP is to prevent us from depending upon modules that often change. Concrete classes change frequently, while abstractions and interfaces change much less. For example, operations like bug fixing, code recompiling, or merging different branches will be much easier.

However, there is more to it. DIP is key to achieving loosely coupled and maintainable systems alongside concepts such as [Polymorphism](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) or [Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection).

Let’s look at the importance of DIP by observing cases where it is sometimes misplaced with other concepts.

[Must Read](https://www.baeldung.com/cs/dip)

Before

```mermaid
classDiagram
    class ClassB {
        // fields, constructor and methods
    }

    class ClassA {
        - ClassB objectB

        + ClassA(ClassB objectB)
        + void invokeClassBMethods()
    }

    ClassB <-- ClassA: contains

```

After

```mermaid
classDiagram
    
    class InterfaceB {
        +method()
    }

    class ClassB {
        // fields, constructor and methods
    }

    class ObjectA {
        - InterfaceB objectB

        + ObjectA(InterfaceB objectB)
        ...
    }

    InterfaceB <|-- ClassB : implements
    InterfaceB <-- ObjectA: contains

```
