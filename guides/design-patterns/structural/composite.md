# Motivation

-   Objects use other objects' fields/methods through embedding
-   Composition lets us make compound objects
    -   E.g., a mathematical expression composed of simple expressions; or
    -   A shape group made of several different shapes
-   Composite design pattern is used to treat both single (scalar) and composite objects uniformly
    -   I.e., Foo and []Foo have common APIs

# Composite

**Composite** is a structural design pattern that lets you compose objects into tree structures and then work with these structures as if they were individual objects by providing the same interface for single objects and composed objects.

# Problem

Imagine you have a picture and you need to calculate the area. You could create a border line around the picture and try to figure out the area by dividing the picture in small geometric shapes. Now you sum up all the areas of the shapes to calculate the total area of the picture. 

Wouldn't it be nice to call a method on the entire picture called **Area()** and internally the program would traverse the entire composition of shapes calling the same method **Area()** on each of the shapes?

# Solution

The idea is that you can have a scalar shape like Circle or Square or you could have a more complex shape that is composed of these scalar ones and you will be able to call the same methods in the complex shape and in the scalar shapes without worrying about how to handle each. The key is to have methods that handle the different use cases.

The greatest benefit of this approach is that you don’t need to care about the concrete classes of objects that compose the tree. You don’t need to know whether an object is a simple circle or a complex polygon. You can treat them all the same via the common interface. When you call a method, the objects themselves pass the request down the tree.

# Code Example

## Graphic Object

```go
package main

import (
	"fmt"
	"strings"
)

type GraphicObject struct {
	Name, Color string
	Children []GraphicObject
}

func (g *GraphicObject) String() string {
	sb := strings.Builder{}
	g.print(&sb, 0)
	return sb.String()
}

func (g *GraphicObject) print(sb *strings.Builder, depth int) {
	sb.WriteString(strings.Repeat("*", depth))
	
	if len(g.Color) > 0 {
		sb.WriteString(g.Color)
		sb.WriteRune(' ')
	}
	
	sb.WriteString(g.Name)
	sb.WriteRune('\n')

	for _, child := range g.Children {
		child.print(sb, depth+1)
	}
}

func NewCircle(color string) *GraphicObject {
	return &GraphicObject{"Circle", color, nil}
}

func NewSquare(color string) *GraphicObject {
	return &GraphicObject{"Square", color, nil}
}

func main() {
	drawing := GraphicObject{"My Drawing", "", nil}
	drawing.Children = append(drawing.Children, *NewSquare("Red"))
	drawing.Children = append(drawing.Children, *NewCircle("Yellow"))
	group := GraphicObject{"Group 1", "", nil}
	group.Children = append(group.Children, *NewCircle("Blue"))
	group.Children = append(group.Children, *NewSquare("Blue"))
	drawing.Children = append(drawing.Children, group)
	fmt.Println(drawing.String())
}
```

## Neural Network

```go
package main

type NeuronInterface interface {
	Iter() []*Neuron
}

type Neuron struct {
	In, Out []*Neuron
}

func (n *Neuron) Iter() []*Neuron {
	return []*Neuron{n}
}

func (n *Neuron) ConnectTo(other *Neuron) {
	n.Out = append(n.Out, other)
	other.In = append(other.In, n)
}

type NeuronLayer struct {
	Neurons []Neuron
}

func (n *NeuronLayer) Iter() []*Neuron {
	result := make([]*Neuron, 0)
	for i := range n.Neurons {
		result = append(result, &n.Neurons[i])
	}
	return result
}

func NewNeuronLayer(count int) *NeuronLayer {
	return &NeuronLayer{make([]Neuron, count)}
}

func Connect(left, right NeuronInterface) {
	for _, l := range left.Iter() {
		for _, r := range right.Iter() {
			l.ConnectTo(r)
		}
	}
}

func main() {
	neuron1, neuron2 := &Neuron{}, &Neuron{}
	layer1, layer2 := NewNeuronLayer(3), NewNeuronLayer(4)
	//neuron1.ConnectTo(&neuron2)
	Connect(neuron1, neuron2)
	Connect(neuron1, layer1)
	Connect(layer2, neuron1)
	Connect(layer1, layer2)
}
```

# Applicability

* Use the Composite pattern when you have to implement a tree-like object structure.
* Use the pattern when you want the client code to treat both simple and complex elements uniformly.

# Pros and Cons

|Pros| Cons |
|--|--|
| You can work with complex tree structures more conveniently: use polymorphism and recursion to your advantage. | It might be difficult to provide a common interface for classes whose functionality differs too much. In certain scenarios, you’d need to overgeneralize the component interface, making it harder to comprehend. |
| _Open/Closed Principle_. You can introduce new element types into the app without breaking the existing code, which now works with the object tree. |  |
