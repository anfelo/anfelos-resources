# Motivation

- Object creation logic becomes too convoluted
- Struct has too many fields, need to initialize all correctly
- Wholesale object creation (non-piecewise, unlike Builder) can be outsourced to
- A separate function (Factory Function, a.k.a. Constructor)
- That may exist in a separate struct (Factory)
  
# Factory

A component responsible solely for the wholesale (not piecewise) creation of objects.

## Prototype Factory

Preconfigured objects created through a factory method. This is useful when creating base objects that will be defined in detail later

# Problem

Most of the times as you build a product things start to get more complex. The classes you created at the beginning start to grow and require a more complex initialization. In this moment you realize that you need to controlling how your classes are being created to ensure the proper configuration.

# Solution

The solution is to have a separate factory method that will create an instance of the class with the proper initialization arguments and logic. The idea is to move the construction login to the factory method and make the factory method part of the interface of the base struct. This way, subclasses can override this method and build the instance with the required logic.

# Code Example

## Factory Method

A function that when called with some initialization parameters, return an instance of the object. This is particularly useful if we have some additional steps of validations that need to happen before the creation of the object. In this case, the factory function is the place to perform all the steps and validations and return the entire object created.

```go
package main

import "fmt"

type Person struct {
	Name string
	Age int
}

// factory method
func NewPerson(name string, age int) *Person {
	return &Person{name, age}
}

func main() {
	// initialize directly
	p := Person{"John", 22}
	fmt.Println(p)
	// use a constructor
	p2 := NewPerson("Jane", 21)
	p2.Age = 30
	fmt.Println(p2)
}
```

## Interface Factory or Abstract Factory

There are time when we want to protect the object that has being created from modification of its properties. For we make the Factory Method, return not the not the instance of the object but an interface. The user can still call the interface methods to retrieve some information about the object or even call some exposed setter to modify the object.

Additionally, we could have two different objects that inherit from the same interface and they could be constructed in the same Factory Method. This could be useful in some cases.

```go
package main

import "fmt"

type Person interface {
	SayHello()
}

type person struct {
	name string
	age int
}

func (p *person) SayHello() {
	fmt.Printf("Hi, my name is %s, I am %d years old\n",
	p.name, p.age)
}

func NewPerson(name string, age int) Person {
	return &person{name, age}
}

func main() {
	// initialize with factory method
	p := NewPerson("James", 34)
	// Can't do somethiong like p.name or p.age
	p.SayHello()
}
```

## Factory Generator

Instead of having a fixed factory method, we could create a method that creates factories functions and allow for a more custom and flexible creation of objects. The user would first create one factory with some properties defined and after call the factory with some other details about the object.

Having the factories stored in variables makes it easy to provide them as parameter to other functions.

```go
package  main

import  "fmt"

type  Employee  struct {
	Name, Position string
	AnnualIncome int
}
// what if we want factories for specific roles?

// functional approach
func  NewEmployeeFactory(
	position string, annualIncome int
) func(name string) *Employee {
	return  func(name string) *Employee {
		return  &Employee{name, position, annualIncome}
	}
}

// structural approach
type  EmployeeFactory  struct {
	Position string
	AnnualIncome int
}

func  NewEmployeeFactory2(position string, annualIncome int) *EmployeeFactory {
	return  &EmployeeFactory{position, annualIncome}
}

func (f *EmployeeFactory) Create(name string) *Employee {
	return  &Employee{name, f.Position, f.AnnualIncome}
}

func  main() {
	developerFactory :=  NewEmployeeFactory("Developer", 60000)
	managerFactory :=  NewEmployeeFactory("Manager", 80000)

	developer :=  developerFactory("Adam")
	fmt.Println(developer)

	manager :=  managerFactory("Jane")
	fmt.Println(manager)

	bossFactory :=  NewEmployeeFactory2("CEO", 100000)
	// can modify post-hoc (further customize the factories)
	bossFactory.AnnualIncome =  110000
	boss := bossFactory.Create("Sam")
	fmt.Println(boss)
}
```

# Applicability

* Use the Factory Method when you don’t know beforehand the exact types and dependencies of the objects your code should work with.
* Use the Factory Method when you want to provide users of your library or framework with a way to extend its internal components.
* Use the Factory Method when you want to save system resources by reusing existing objects instead of rebuilding them each time.
* Use the Abstract Factory when your code needs to work with various families of related products, but you don’t want it to depend on the concrete classes of those products—they might be unknown beforehand or you simply want to allow for future extensibility.

# Pros and Cons

| Pros | Cons |
|--|--|
| You avoid tight coupling between the creator and the concrete products. | The code may become more complicated since you need to introduce a lot of new subclasses to implement the pattern. The best case scenario is when you’re introducing the pattern into an existing hierarchy of creator classes. |
| _Single Responsibility Principle_. You can move the product creation code into one place in the program, making the code easier to support. |  |
| _Open/Closed Principle_. You can introduce new types of products into the program without breaking existing client code. |  |
