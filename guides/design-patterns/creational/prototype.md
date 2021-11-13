# Motivation

-   Complicated objects aren't designed from scratch
    -   They reiterate existing designs
-   An existing (partially or fully constructed) design is a Prototype
-   We make a copy of the prototype and customize it
    -   Requires "deep copy"
-   We make the cloning convenient (e.g. via a Factory)

# Prototype

A partially or fully initialized object that you copy (clone) and make use of.

**Prototype**  is a creational design pattern that lets you copy existing objects without making your code dependent on their classes.

## Deep Copy

When copying an object we need to make sure we handle the pointers to its internal properties. Some properties might be pointers to a value and when trying to modify that value, we might end up modifying the original object.

# Problem

Making a copy of an object is not that simple. Sometimes those objects have hidden properties or private properties/methods. In the process of going property by property copying the objects' internals you will have a huge coupling and if the object changes someday you need to change the copying process as well.

# Solution

The solution is simple, create objects that expose a copy method in their interface. Let the object/class deal with the copying of itself as it closely relates to it. Objects that implement the copy method are called prototypes. When creating a new object from the prototype just call the copy method and proceed to change the public properties as needed.

# Code Examples

## Copy Method

We can add a DeepCopy method to our objects so they can handle the creation of deep structures within our object. This way we can copy the underlying values of pointers in our object.

```go
package main

import "fmt"

type Address struct {
	StreetAddress, City, Country string
}

func (a *Address) DeepCopy() *Address {
	return &Address{
		a.StreetAddress,
		a.City,
		a.Country,
	}
}

type Person struct {
	Name string
	Address *Address
	Friends []string
}

func (p *Person) DeepCopy() *Person {
	q := *p // copies Name
	q.Address = p.Address.DeepCopy()
	copy(q.Friends, p.Friends)
	return &q
}

func main() {
	john := Person{"John",
	&Address{"123 London Rd", "London", "UK"},
	[]string{"Chris", "Matt"}}
	jane := john.DeepCopy()
	jane.Name = "Jane"
	jane.Address.StreetAddress = "321 Baker St"
	jane.Friends = append(jane.Friends, "Angela")
	fmt.Println(john, john.Address)
	fmt.Println(jane, jane.Address)
}
```

## Copy Through Serialization

Another way to perform a deep copy without having to create many methods to handle pointers is by encoding the object into something like bytes array or JSON and then decoding that byte array into the new object that is being created.

```go
package main

import (
	"bytes"
	"encoding/gob"
	"fmt"
)

type Address struct {
	StreetAddress, City, Country string
}

type Person struct {
	Name string
	Address *Address
	Friends []string
}

func (p *Person) DeepCopy() *Person {
	// note: no error handling below
	b := bytes.Buffer{}
	e := gob.NewEncoder(&b)
	_ = e.Encode(p)
	// peek into structure
	fmt.Println(string(b.Bytes()))
	d := gob.NewDecoder(&b)
	result := Person{}
	_ = d.Decode(&result)
	return &result
}

func main() {
	john := Person{"John",
	&Address{"123 London Rd", "London", "UK"},
	[]string{"Chris", "Matt", "Sam"}}
	jane := john.DeepCopy()
	jane.Name = "Jane"
	jane.Address.StreetAddress = "321 Baker St"
	jane.Friends = append(jane.Friends, "Jill")
	fmt.Println(john, john.Address)
	fmt.Println(jane, jane.Address)
}
```

## Prototype Factory

This can be a factory method that takes a prototype object as a parameter. This prototype object can be a base object missing configuration of some fields. Make it easy and convenient to create new objects that share some common property values.

```go
package main

import (
	"bytes"
	"encoding/gob"
	"fmt"
)

type Address struct {
	Suite int
	StreetAddress, City string
}

type Employee struct {
	Name string
	Office Address
}

func (p *Employee) DeepCopy() *Employee {
	// note: no error handling below
	b := bytes.Buffer{}
	e := gob.NewEncoder(&b)
	_ = e.Encode(p)
	// peek into structure
	//fmt.Println(string(b.Bytes()))
	d := gob.NewDecoder(&b)
	result := Employee{}
	_ = d.Decode(&result)
	return &result
}
// employee factory

// either a struct or some functions
var mainOffice = Employee{
	"",
	Address{0, "123 East Dr", "London"},
}

var auxOffice = Employee{
	"", 
	Address{0, "66 West Dr", "London"},
}

// utility method for configuring emp
// ↓ lowercase
func newEmployee(proto *Employee, name string, suite int) *Employee {
	result := proto.DeepCopy()
	result.Name = name
	result.Office.Suite = suite
	return result
}

func NewMainOfficeEmployee(name string, suite int) *Employee {
	return newEmployee(&mainOffice, name, suite)
}

func NewAuxOfficeEmployee(name string, suite int) *Employee {
	return newEmployee(&auxOffice, name, suite)
}

func main() {
	john := NewMainOfficeEmployee("John", 100)
	jane := NewAuxOfficeEmployee("Jane", 200)
	fmt.Println(john)
	fmt.Println(jane)
}
```

# Applicability

* Use the Prototype pattern when your code shouldn’t depend on the concrete classes of objects that you need to copy.
* Use the pattern when you want to reduce the number of subclasses that only differ in the way they initialize their respective objects. Somebody could have created these subclasses to be able to create objects with a specific configuration.

# Pros and Cons

| Pros | Cons |
|--|--|
| You can clone objects without coupling to their concrete classes. | Cloning complex objects that have circular references might be very tricky. |
| You can get rid of repeated initialization code in favor of cloning pre-built prototypes. |  |
| You can produce complex objects more conveniently. |  |
| You get an alternative to inheritance when dealing with configuration presets for complex objects. |  |
