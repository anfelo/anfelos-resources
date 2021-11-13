# Overview

-   Design principles introduced by Robert C. Martin (uncle Bob)
-   Frequently referenced in design patterns literature

# Principles

-   **S**ingle Responsibility Principle (SRP)
-   **O**pen-Closed Principle (OCP)
-   **L**iskov Substitution Principle (LSP)
-   **I**nterface Segregation Principle (ISP)
-   **D**ependency Inversion Principle (DIP)

## Single Responsibility Principle (SRP)

A type/object/method should only have a single primary responsibility. A somewhat related concept is the  _Separation of Concerns_  which states that different problems that a system solves need to be separated in their own constructs/modules/packages.

The anti pattern here is the  _God Object_. This is an object or method that does main of the tasks that the system requires making it hard to debug and maintain. This also reduces reusability.

## Open-Closed Principle (OCP)

Types should be open for extension and closed for modification. The idea is to avoid modification of pieces of code that have already being tested. Create base objects that can be extended for different use cases.

## Liskov Substitution Principle (LSP)

If you have a method that receives an argument of some base type, it should also work with an argument that extends that base class. This principle is applicable to OOP languages primarily.

## Interface Segregation Principle (ISP)

An interface should not be too large. You should compose the functionality with multiple interfaces. You will end up with granular functionality. If an interface has too many functionalities, there might be a case where some object that extends the interface ends up filled with unnecessary functionality. (YAGNI - You Ain't Going to Need It)

## Dependency Inversion Principle (DIP)

High level modules (HLM) should not depend on low level modules (LLM), both should depend on abstractions. In some programming languages this can be achieved with abstract classes instead of concrete classes.