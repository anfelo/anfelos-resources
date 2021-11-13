# Motivation

-   Bridge prevents a "Cartesian product" complexity explosion
-   Example:
    -   Common type ThreadScheduler
    -   Can be preemptive or cooperative
    -   Can run on Windows or Unix
    -   End up with a 2x2 scenario: WindowsPTS, UnixPTS, WindowsCTS, UnixCTS
-   Bridge pattern avoids the entity explosion

# Bridge

A mechanism that decouples an interface (hierarchy) from an implementation (hierarchy) The **Bridge** pattern attempts to solve the complexity explosion problem by switching from inheritance to the object composition. What this means is that you extract one of the dimensions into a separate class hierarchy, so that the original classes will reference an object of the new hierarchy, instead of having all of its state and behaviors within one class.

**Bridge** is a structural design pattern that lets you split a large class or a set of closely related classes into two separate hierarchies—abstraction and implementation—which can be developed independently of each other.

# Problem

A simple example is the following. Imagine that you have a base class **Button** for your UI components, and you have inherited this class for two different designs in you app, the **CircleButton** and **RectangleButton**. Now you want to theme your buttons with a dark of light colors, and you decide to create more classes that inherit from the base ones. You end up with **DarkCircleButton**, **LightCircleButton**, **DarkRectangleButton** and **LightRectangleButton**. 

Adding new shapes and themes to your UI system starts to become a mess as your amount of classes grow exponentially.

# Solution

The solution is to break the hierarchies using the object composition instead. This means to hold a reference to the other classes instead of inheriting from them. In the example above this means that the **Button** should contain two fields, the border-shape and the theme and instead of creating a specific class for each shape and color you can create some more generic classes like, **DarkTheme**, **LightTheme**, **CircleBorder** and **RectangleBorder**. Now, when adding a new theme color or new border shape you just need to create one class and reference it when building a new button.

# Code Example

```go
package main

import "fmt"

type Renderer interface {
	RenderCircle(radius float32)
}

type VectorRenderer struct {
}

func (v *VectorRenderer) RenderCircle(radius float32) {
	fmt.Println("Drawing a circle of radius", radius)
}

type RasterRenderer struct {
	Dpi int
}

func (r *RasterRenderer) RenderCircle(radius float32) {
	fmt.Println("Drawing pixels for circle of radius", radius)
}

type Circle struct {
	renderer Renderer
	radius float32
}

func (c *Circle) Draw() {
	c.renderer.RenderCircle(c.radius)
}

func NewCircle(renderer Renderer, radius float32) *Circle {
	return &Circle{renderer: renderer, radius: radius}
}

func (c *Circle) Resize(factor float32) {
	c.radius *= factor
}

func main() {
	raster := RasterRenderer{}
	vector := VectorRenderer{}
	circleVector := NewCircle(&vector, 5)
	circleRaster := NewCircle(&raster, 5)
	circleVector.Draw()
	circleRaster.Draw()
}
```

# Applicability

* Use the Bridge pattern when you want to divide and organize a monolithic class that has several variants of some functionality (for example, if the class can work with various database servers).
* Use the pattern when you need to extend a class in several orthogonal (independent) dimensions.
* Use the Bridge if you need to be able to switch implementations at runtime.

# Pros and Cons

|Pros|Cons  |
|--|--|
| You can create platform-independent classes and apps. | You might make the code more complicated by applying the pattern to a highly cohesive class. |
| The client code works with high-level abstractions. It isn’t exposed to the platform details. |  |
| _Open/Closed Principle_. You can introduce new abstractions and implementations independently from each other. |  |
| _Single Responsibility Principle_. You can focus on high-level logic in the abstraction and on platform details in the implementation. |  |
