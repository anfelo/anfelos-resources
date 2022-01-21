# Motivation

-   Need to define a new operation on an entire type hierarchy
    -   E.g., given a document model (list, paragraphs, etc.), we want to add printing functionality
-   Do not want to keep modifying every type in the hierarchy
-   Want to have the new functionality separate (SRP)
-   This approach is often used for traversal
    -   Alternative to Iterator
    -   Hierarchy members help you traverse themselves

# Visitor

A pattern where a component (visitor) is allowed to traverse the entire hierarchy of types. Implemented by propagating a single Accept() method throughout the entire hierarchy.

# Problem

When we have a tree like of graph objects structure and we want to be able to traverse the entire hierarchy without modifying the classes.

# Solution 

We can create a new object or interface called the *Visitor*. We add methods to the visitor to deal with each of the nodes and for each of those methods we pass the node class as an argument.
Now when trying to decide which class needs to be visited, the Visitor pattern relies on a technique called the **Double Dispatch** which helps execute the right method. This way the client does not have to select which method to call. We should implement an Accept method in the node class that receives as a parameter the visitor. This method calls the right visitor method for that class.

# Code Examples

## Classic Visitor

```go
package visitor

import (
	"fmt"
	"strings"
)

type ExpressionVisitor interface {
	VisitDoubleExpression(de *DoubleExpression)
	VisitAdditionExpression(ae *AdditionExpression)
}

type Expression interface {
	Accept(ev ExpressionVisitor)
}

type DoubleExpression struct {
	value float64
}

func (d *DoubleExpression) Accept(ev ExpressionVisitor) {
	ev.VisitDoubleExpression(d)
}

type AdditionExpression struct {
	left, right Expression
}

func (a *AdditionExpression) Accept(ev ExpressionVisitor) {
	ev.VisitAdditionExpression(a)
}

type ExpressionPrinter struct {
	sb strings.Builder
}

func (e *ExpressionPrinter) VisitDoubleExpression(de *DoubleExpression) {
	e.sb.WriteString(fmt.Sprintf("%g", de.value))
}

func (e *ExpressionPrinter) VisitAdditionExpression(ae *AdditionExpression) {
	e.sb.WriteString("(")
	ae.left.Accept(e)
	e.sb.WriteString("+")
	ae.right.Accept(e)
	e.sb.WriteString(")")
}

func NewExpressionPrinter() *ExpressionPrinter {
	return &ExpressionPrinter{strings.Builder{}}
}

func (e *ExpressionPrinter) String() string {
	return e.sb.String()
}

func main() {
	// 1+(2+3)
	e := &AdditionExpression{
		&DoubleExpression{1},
		&AdditionExpression{
			left: &DoubleExpression{2},
			right: &DoubleExpression{3},
		},
	}
	ep := NewExpressionPrinter()
	ep.VisitAdditionExpression(e)
	fmt.Println(ep.String())
}
```

## Intrusive Visitor

One implementation of the visitor pattern is one where some action needs to be implemented on every interface in order to traverse the entire hierarchy.

```go
package main

import (
	"fmt"
	"strings"
)

// StringsBuilder 'visits' all the interfaces
// It's intrusive because it has to be implemented on each interface
type Expression interface {
	Print(sb *strings.Builder)
}

type DoubleExpression struct {
	value float64
}

func (d *DoubleExpression) Print(sb *strings.Builder) {
	sb.WriteString(fmt.Sprintf("%g", d.value))
}

type AdditionExpression struct {
	left, right Expression
}

func (a *AdditionExpression) Print(sb *strings.Builder) {
	sb.WriteString("(")
	a.left.Print(sb)
	sb.WriteString("+")
	a.right.Print(sb)
	sb.WriteString(")")
}

func main() {
	// 1+(2+3)
	e := AdditionExpression{
		&DoubleExpression{1},
		&AdditionExpression{
			left: &DoubleExpression{2},
			right: &DoubleExpression{3},
		},
	}
	sb := strings.Builder{}
	e.Print(&sb)
	fmt.Println(sb.String())
}
```

## Reflective Visitor

The reflective visitor needs to check the internals of each interface to understand their nature and handle the next action accordingly. The down side is that every time there is a new interface added to the hierarchy we need to go and modify the action handler so the case is covered. This breaks the OCP.

```go
package visitor

import (
	"fmt"
	"strings"
)

type Expression interface {
// nothing here!
}

type DoubleExpression struct {
	value float64
}

type AdditionExpression struct {
	left, right Expression
}

func Print(e Expression, sb *strings.Builder) {
	if de, ok := e.(*DoubleExpression); ok {
		sb.WriteString(fmt.Sprintf("%g", de.value))
	} else if ae, ok := e.(*AdditionExpression); ok {
		sb.WriteString("(")
		Print(ae.left, sb)
		sb.WriteString("+")
		Print(ae.right, sb)
		sb.WriteString(")")
	}
	// breaks OCP
	// will work incorrectly on missing case
}

func main() {
	// 1+(2+3)
	e := &AdditionExpression{
		&DoubleExpression{1},
		&AdditionExpression{
			left: &DoubleExpression{2},
			right: &DoubleExpression{3},
		},
	}
	sb := strings.Builder{}
	Print(e, &sb)
	fmt.Println(sb.String())
}
```

## Consideration on Dispatch

-   Which function to call?
-   **Single dispatch:** depends on name of request and type of receiver
-   **Double dispatch:** depends on name of request and type of two receivers (type of visitor, type of element being visited)

# Applicability

* Use the Visitor when you need to perform an operation on all elements of a complex object structure (for example, an object tree).
* Use the Visitor to clean up the business logic of auxiliary behaviors.
* Use the pattern when a behavior makes sense only in some classes of a class hierarchy, but not in others.

# Pros and Cons

| Pros | Cons |
|--|--|
| _Open/Closed Principle_. You can introduce a new behavior that can work with objects of different classes without changing these classes. | You need to update all visitors each time a class gets added to or removed from the element hierarchy. |
| _Single Responsibility Principle_. You can move multiple versions of the same behavior into the same class. | Visitors might lack the necessary access to the private fields and methods of the elements that they’re supposed to work with. |
| A visitor object can accumulate some useful information while working with various objects. This might be handy when you want to traverse some complex object structure, such as an object tree, and apply the visitor to each object of this structure. |  |
