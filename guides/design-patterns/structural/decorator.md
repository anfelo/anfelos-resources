# Motivation

-   Want to augment an object with additional functionality
-   Do not want to rewrite or alter existing code (OCP)
-   Want to keep new functionality separate (SRP)
-   Need to be able to interact with existing structures
-   Solution: embed the decorated object and provide additional functionality

# Decorator

Facilitates the addition of behaviors to individual objects through embedding.

# Problem

Imagine you have a **Modal** that you use around your entire application. Now you need to create a different **Modal** with some alter look and feel. Maybe you need to create a **FullSizedModal** and another **MobileModal**. After that you need to create a themed variation of these modals like **DarkFullSizedModal**, **LightFullSizedModal**. You can already see how your modal library starts to grow quick.

# Solution

You can create variations of the modals by using a more generic wrapper object like **SizedModal** and adding a property to this modal called **Size** or you could create another variation called **ThemedModal** with a **Theme** property. This allows us to use the more advanced variations of the modal without altering the behavior or style of the base modal.

# Code Example

```go
package main

import "fmt"

type Shape interface {
	Render() string
}

type Circle struct {
	Radius float32
}

func (c *Circle) Render() string {
	return fmt.Sprintf("Circle of radius %f", c.Radius)
}

func (c *Circle) Resize(factor float32) {
	c.Radius *= factor
}

type Square struct {
	Side float32
}

func (s *Square) Render() string {
	return fmt.Sprintf("Square with side %f", s.Side)
}

// possible, but not generic enough
type ColoredSquare struct {
	Square
	Color string
}

type ColoredShape struct {
	Shape Shape
	Color string
}

func (c *ColoredShape) Render() string {
	return fmt.Sprintf("%s has the color %s",
	c.Shape.Render(), c.Color)
}

type TransparentShape struct {
	Shape Shape
	Transparency float32
}

func (t *TransparentShape) Render() string {
	return fmt.Sprintf("%s has %f%% transparency",
	t.Shape.Render(), t.Transparency*100.0)
}

func main() {
	circle := Circle{2}
	fmt.Println(circle.Render())
	redCircle := ColoredShape{&circle, "Red"}
	fmt.Println(redCircle.Render())
	rhsCircle := TransparentShape{&redCircle, 0.5}
	fmt.Println(rhsCircle.Render())
}
```

## Multiple Aggregation

In this case we have two different objects that share some fields, however, they have different behaviors. Now imagine we have a third object that aggregates the other two object behaviors and fields. This new object can perform all of the actions that object 1 and object 2 can perform but we don't want the other similar properties to collide. For this we can create a decorator that aggregates both objects and provides the same API, handling the collision of similar properties.

```go
package main

import "fmt"

type Aged interface {
	Age() int
	SetAge(age int)
}

type Bird struct {
	age int
}

func (b *Bird) Age() int { return b.age }

func (b *Bird) SetAge(age int) { b.age = age }

func (b *Bird) Fly() {
	if b.age >= 10 {
		fmt.Println("Flying!")
	}
}

type Lizard struct {
	age int
}

func (l *Lizard) Age() int { return l.age }

func (l *Lizard) SetAge(age int) { l.age = age }

func (l *Lizard) Crawl() {
	if l.age < 10 {
		fmt.Println("Crawling!")
	}
}

// Decorator object
type Dragon struct {
	bird Bird
	lizard Lizard
}

func (d *Dragon) Age() int {
	return d.bird.age
}

func (d *Dragon) SetAge(age int) {
	d.bird.SetAge(age)
	d.lizard.SetAge(age)
}

func (d *Dragon) Fly() {
	d.bird.Fly()
}

func (d *Dragon) Crawl() {
	d.lizard.Crawl()
}

func NewDragon() *Dragon {
	return &Dragon{Bird{}, Lizard{}}
}

func main() {
	d := NewDragon()
	d.SetAge(10)
	d.Fly()
	d.Crawl()
}
```

# Applicability

* Use the Decorator pattern when you need to be able to assign extra behaviors to objects at runtime without breaking the code that uses these objects.
* Use the pattern when it’s awkward or not possible to extend an object’s behavior using inheritance.

# Pros and Cons

|Pros| Cons |
|--|--|
| You can extend an object’s behavior without making a new subclass. | It’s hard to remove a specific wrapper from the wrappers stack. |
| You can add or remove responsibilities from an object at runtime. | It’s hard to implement a decorator in such a way that its behavior doesn’t depend on the order in the decorators stack. |
| You can combine several behaviors by wrapping an object into multiple decorators. | The initial configuration code of layers might look pretty ugly. |
|_Single Responsibility Principle_. You can divide a monolithic class that implements many possible variants of behavior into several smaller classes. |  |
