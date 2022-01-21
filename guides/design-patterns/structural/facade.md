# Motivation

-   Balancing complexity and presentation/usability
-   Typical home
    -   Many subsystems (electrical, sanitation)
    -   Complex internal structure (e.g. floor layers)
    -   End user is not exposed to internals
-   Same with software
    -   Many systems working to provide flexibility, but...
    -   API consumers want it to 'just work'

# Facade

**Facade** is a structural design pattern that provides a simplified interface to a library, a framework, or any other complex set of classes.

# Problem

Imagine enter a coffee shop and they give you a mug and some coffee grains. They let you grid your coffee with their machine, they let you use the espresso machine to extract the coffee and finally add some cream. they do not have baristas, you need to know how all the process and the machines work in order to make your own coffee.

# Solution

User don't want to know how everything works internally more importantly they do not want to do all the work. They just want a nice API they can call to start using the library right away.

You just want to ask the barista, 'Please make me a Cappuccino' and there you go. 

# Code Example

```go
package main

type Buffer struct {
	width, height int
	buffer []rune
}

func NewBuffer(width, height int) *Buffer {
	return &Buffer{width, height,
		make([]rune, width*height)}
}

func (b *Buffer) At(index int) rune {
	return b.buffer[index]
}

type Viewport struct {
	buffer *Buffer
	offset int
}

func NewViewport(buffer *Buffer) *Viewport {
	return &Viewport{buffer: buffer}
}

func (v *Viewport) GetCharacterAt(index int) rune {
	return v.buffer.At(v.offset + index)
}

// a facade over buffers and viewports
type Console struct {
	buffers []*Buffer
	viewports []*Viewport
	offset int
}

func NewConsole() *Console {
	b := NewBuffer(10, 10)
	v := NewViewport(b)
	return &Console{[]*Buffer{b}, []*Viewport{v}, 0}
}

func (c *Console) GetCharacterAt(index int) rune {
	return c.viewports[0].GetCharacterAt(index)
}

func main() {
	c := NewConsole()
	c.GetCharacterAt(1)
}
```

# Applicability

* Use the Facade pattern when you need to have a limited but straightforward interface to a complex subsystem.
* Use the Facade when you want to structure a subsystem into layers.

# Pros and Cons

|Pros| Cons |
|--|--|
| You can isolate your code from the complexity of a subsystem. | A facade can become [a god object](https://en.wikipedia.org/wiki/God_object) coupled to all classes of an app. |
