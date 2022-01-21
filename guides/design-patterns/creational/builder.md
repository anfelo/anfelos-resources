# Motivation

-   Some objects are simple and can be created in a single constructor call
-   Other objects require a lot of ceremony to create
-   Having a factory function with 10 arguments is not productive
-   Instead, opt for piecewise (piece-by-piece) construction
-   Builder provides an API for constructing an object step-by-step

# Builder

**Builder** is a creational design pattern that lets you construct complex objects step by step.

## Extra

A pattern that goes side by side with the builder pattern is the fluent interface, which makes it easy to chain methods and build the objects with out creating a bunch of variables. This is done by having all the methods return a pointer to the object that is being built.

# Problem

Classes normally start very concise and small and they grow as new requirements come along. You start to see that the constructor of your class grows in lines and in conditions and steps that need to happen to instantiate your class. Now your class is unmaintainable no body wants to deal with any related bugs of refactors.

# Solution

The Builder pattern suggests that you extract the object construction code out of its own class and move it to separate objects called _builders_. This pattern leverages on the Single Responsibility Principle and consists on separating the complex constructor into many concise builder methods.
To create an object, you execute a series of these steps on a builder object. The important part is that you don’t need to call all of the steps. You can call only those steps that are necessary for producing a particular configuration of an object.

# Code Examples

```go
package main

import (
	"fmt"
	"strings"
)

const (
	indentSize = 2
)

type HtmlElement struct {
	name, text string
	elements []HtmlElement
}

func (e *HtmlElement) String() string {
	return e.string(0)
}

func (e *HtmlElement) string(indent int) string {
	sb := strings.Builder{}
	i := strings.Repeat(" ", indentSize*indent)
	sb.WriteString(fmt.Sprintf("%s<%s>\n", i, e.name))
	if len(e.text) > 0 {
		sb.WriteString(strings.Repeat(" ",
		indentSize*(indent+1)))
		sb.WriteString(e.text)
		sb.WriteString("\n")
	}

	for _, el := range e.elements {
		sb.WriteString(el.string(indent + 1))
	}
	sb.WriteString(fmt.Sprintf("%s</%s>\n", i, e.name))
	return sb.String()
}

type HtmlBuilder struct {
	rootName string
	root HtmlElement
}

func NewHtmlBuilder(rootName string) *HtmlBuilder {
	b := HtmlBuilder{rootName,
	HtmlElement{rootName, "", []HtmlElement{}}}
	return &b
}

func (b *HtmlBuilder) String() string {
	return b.root.String()
}

func (b *HtmlBuilder) AddChild(
	childName, childText string) {
	e := HtmlElement{childName, childText, []HtmlElement{}}
	b.root.elements = append(b.root.elements, e)
}

func (b *HtmlBuilder) AddChildFluent(
	childName, childText string) *HtmlBuilder {
	e := HtmlElement{childName, childText, []HtmlElement{}}
	b.root.elements = append(b.root.elements, e)
	return b
}

func main() {
// Built-in StringBuilder
	hello := "hello"
	sb := strings.Builder{}
	sb.WriteString("<p>")
	sb.WriteString(hello)
	sb.WriteString("</p>")
	fmt.Printf("%s\n", sb.String())
	words := []string{"hello", "world"}
	sb.Reset()
	// <ul><li>...</li><li>...</li><li>...</li></ul>'
	sb.WriteString("<ul>")
	for _, v := range words {
		sb.WriteString("<li>")
		sb.WriteString(v)
		sb.WriteString("</li>")
	}
	sb.WriteString("</ul>")
	fmt.Println(sb.String())
	// HtmlBuilder (Fluent interfaces: returns a pointer to the object being modified)
	b := NewHtmlBuilder("ul")
	b.AddChildFluent("li", "hello").
	AddChildFluent("li", "world")
	fmt.Println(b.String())
}
```


## Builder Facets

There are cases where the objects we are trying to build are complicated and require many builders to be composed. For this case we can have many builders that aggregate the base builder and jump from one builder to the other as they are also a base builder. This way we can use SOC (Separation of Concerns) and have a fluent builder.

```go
package main

import "fmt"

type Person struct {
	StreetAddress, Postcode, City string
	CompanyName, Position string
	AnnualIncome int
}

type PersonBuilder struct {
	person *Person // needs to be inited
}

func NewPersonBuilder() *PersonBuilder {
	return &PersonBuilder{&Person{}}
}

func (it *PersonBuilder) Build() *Person {
	return it.person
}

func (it *PersonBuilder) Works() *PersonJobBuilder {
	return &PersonJobBuilder{*it}
}

func (it *PersonBuilder) Lives() *PersonAddressBuilder {
	return &PersonAddressBuilder{*it}
}

type PersonJobBuilder struct {
	PersonBuilder
}

func (pjb *PersonJobBuilder) At(companyName string) *PersonJobBuilder {
	pjb.person.CompanyName = companyName
	return pjb
}

func (pjb *PersonJobBuilder) AsA(position string) *PersonJobBuilder {
	pjb.person.Position = position
	return pjb
}

func (pjb *PersonJobBuilder) Earning(annualIncome int) *PersonJobBuilder {
	pjb.person.AnnualIncome = annualIncome
	return pjb
}

type PersonAddressBuilder struct {
	PersonBuilder
}

func (it *PersonAddressBuilder) At(streetAddress string) *PersonAddressBuilder {
	it.person.StreetAddress = streetAddress
	return it
}

func (it *PersonAddressBuilder) In(city string) *PersonAddressBuilder {
	it.person.City = city
	return it
}

func (it *PersonAddressBuilder) WithPostcode(postcode string) *PersonAddressBuilder {
	it.person.Postcode = postcode
	return it
}

func main() {
	pb := NewPersonBuilder()
	pb.
		Lives().
		At("123 London Road").
		In("London").
		WithPostcode("SW12BC").
		Works().
		At("Fabrikam").
		AsA("Programmer").
		Earning(123000)
	person := pb.Build()
	fmt.Println(*person)
}
```

## Builder Parameter

Having a builder does not prevent the user from creating its own object from the interface specified. To force the user to use the builder we can use a builder function as a parameter to some action that will take care of building the object and perform some action internally without exposing the object that was created.

```go
package main

import "strings"

type email struct {
	from, to, subject, body string
}

type EmailBuilder struct {
	email email
}

func (b *EmailBuilder) From(from string) *EmailBuilder {
	if !strings.Contains(from, "@") {
		panic("email should contain @")
	}
	b.email.from = from
	return b
}

func (b *EmailBuilder) To(to string) *EmailBuilder {
	b.email.to = to
	return b
}

func (b *EmailBuilder) Subject(subject string) *EmailBuilder {
	b.email.subject = subject
	return b
}

func (b *EmailBuilder) Body(body string) *EmailBuilder {
	b.email.body = body
	return b
}

func sendMailImpl(email *email) {
	// actually ends the email
}

type build func(*EmailBuilder) func SendEmail(action build) {
	builder := EmailBuilder{}
	action(&builder)
	sendMailImpl(&builder.email)
}

func main() {
	// The actual email that is being send is hidden from the user
	SendEmail(func(b *EmailBuilder) {
		b.
			From("foo@bar.com").
			To("bar@baz.com").
			Subject("Meeting").
			Body("Hello, do you want to meet?")
	})
}
```

## Functional Builder

Another approach is to delay the build action of the object, by stacking all the actions that need to happen to create the object and finally, when all the actions have being stored, we can call the Build method to execute all the actions and build the object. This is a more functional approach and allows us to extend the creation of the object easier with out having to create more builders.

```go
package main

import "fmt"

type Person struct {
	name, position string
}

type personMod func(*Person)

type PersonBuilder struct {
	actions []personMod
}

func (b *PersonBuilder) Called(name string) *PersonBuilder {
	b.actions = append(b.actions, func(p *Person) {
		p.name = name
	})
	return b
}

func (b *PersonBuilder) Build() *Person {
	p := Person{}
	for _, a := range b.actions {
		a(&p)
	}
	return &p
}

// extend PersonBuilder
func (b *PersonBuilder) WorksAsA(position string) *PersonBuilder {
	b.actions = append(b.actions, func(p *Person) {
		p.position = position
	})
	return b
}

func main() {
	b := PersonBuilder{}
	p := b.Called("Dmitri").WorksAsA("dev").Build()
	fmt.Println(*p)
}
```

# Applicability

* Use the Builder pattern to get rid of a “telescopic constructor”.
* Use the Builder pattern when you want your code to be able to create different representations of some product (for example, stone and wooden houses).
* Use the Builder to construct Composite trees or other complex objects.

# Pros and Cons

| Pros | Cons |
|--|--|
| You can construct objects step-by-step, defer construction steps or run steps recursively. | The overall complexity of the code increases since the pattern requires creating multiple new classes. |
| You can reuse the same construction code when building various representations of products. |  |
| _Single Responsibility Principle_. You can isolate complex construction code from the business logic of the product. |  |
