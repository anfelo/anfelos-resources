# Motivation

- Adapters in the real world are all over the place (e.g. power adapters)
- We cannot modify our gadgets to support every possible interface
- Thus, we use a special device (an adapter) to give us the interface we require from the interface we have

# Adapter

A construct which adapts an existing interface **X** to conform to the required interface **Y**. An adapter wraps one of the objects to hide the complexity of conversion happening behind the scenes. The wrapped object isn’t even aware of the adapter. For example, you can wrap an object that operates in meters and kilometers with an adapter that converts all of the data to imperial units such as feet and miles.

# Problem

The most common scenario is when your application or system tries to use a third party library that requires a different structure of data than how it works around your entire system, in order to work. It can be in the form of a different structure but it can be also in the form of different formats or even paradigm.

# Solution

The solution is to create an **adapter** object (a.k.a the **wrapper**). This **adapter** is in charge of mutating the interface of one object so that another object can communicate or use its methods.

The way the **adapter** works is by wrapping one of the objects to hide the complexity of the conversion happening. This way other objects can communicate with the wrapped object with the same interface without knowing that a translation/conversion is happening. Here is how it works:

1.  The adapter gets an interface, compatible with one of the existing objects.
2.  Using this interface, the existing object can safely call the adapter’s methods.
3.  Upon receiving a call, the adapter passes the request to the second object, but in a format and order that the second object expects.

# Structure

There are two main types of adapter implementations, the *object adapter* and the *class adapter*. Let's take a look at the *object adapter* first:

## Object Adapter
The adapter implements the interface of one object and wraps the other one.

1.  The  **Client**  is a class that contains the existing business logic of the program.
    
2.  The  **Client Interface**  describes a protocol that other classes must follow to be able to collaborate with the client code.
    
3.  The  **Service**  is some useful class (usually 3rd-party or legacy). The client can’t use this class directly because it has an incompatible interface.
    
4.  The  **Adapter**  is a class that’s able to work with both the client and the service: it implements the client interface, while wrapping the service object. The adapter receives calls from the client via the adapter interface and translates them into calls to the wrapped service object in a format it can understand.
    
5.  The client code doesn’t get coupled to the concrete adapter class as long as it works with the adapter via the client interface. Thanks to this, you can introduce new types of adapters into the program without breaking the existing client code. This can be useful when the interface of the service class gets changed or replaced: you can just create a new adapter class without changing the client code.

## Class Adapter
This implementation uses multiple inheritance to allow the adapter get the methods from both, the service and the client, without wrapping one or the other.

1. The **Class Adapter** inherits the behavior from both, the client and the service and the implementation details happen within the overridden methods. 

# Code Example

```go
package main

import (
	"crypto/md5"
	"encoding/json"
	"fmt"
	"strings"
)

func minmax(a, b int) (int, int) {
	if a < b {
		return a, b
	} else {
		return b, a
	}
}
// ↑↑↑ utility functions

type Line struct {
	X1, Y1, X2, Y2 int
}

type VectorImage struct {
	Lines []Line
}

func NewRectangle(width, height int) *VectorImage {
	width -= 1
	height -= 1
	return &VectorImage{[]Line{
		Line{0, 0, width, 0},
		Line{0, 0, 0, height},
		Line{width, 0, width, height},
		Line{0, height, width, height}}}
}
// ↑↑↑ the interface you're given

// ↓↓↓ the interface you have
type Point struct {
	X, Y int
}

type RasterImage interface {
	GetPoints() []Point
}

func DrawPoints(owner RasterImage) string {
	maxX, maxY := 0, 0
	points := owner.GetPoints()
	for _, pixel := range points {
		if pixel.X > maxX {
			maxX = pixel.X
		}

		if pixel.Y > maxY {
			maxY = pixel.Y
		}
	}
	maxX += 1
	maxY += 1

	// preallocate
	data := make([][]rune, maxY)
	for i := 0; i < maxY; i++ {
		data[i] = make([]rune, maxX)
		for j := range data[i] {
			data[i][j] = ' '
		}
	}

	for _, point := range points {
		data[point.Y][point.X] = '*'
	}
	b := strings.Builder{}
	for _, line := range data {
		b.WriteString(string(line))
		b.WriteRune('\n')
	}
	return b.String()
}

// problem: I want to print a RasterImage
// but I can only make a VectorImage
type vectorToRasterAdapter struct {
	points []Point
}

var pointCache = map[[16]byte][]Point{}

func (a *vectorToRasterAdapter) addLine(line Line) {
	left, right := minmax(line.X1, line.X2)
	top, bottom := minmax(line.Y1, line.Y2)
	dx := right - left
	dy := line.Y2 - line.Y1
	if dx == 0 {
		for y := top; y <= bottom; y++ {
			a.points = append(a.points, Point{left, y})
		}
	} else if dy == 0 {
		for x := left; x <= right; x++ {
			a.points = append(a.points, Point{x, top})
		}
	}
	fmt.Println("generated", len(a.points), "points")
}

func (a *vectorToRasterAdapter) addLineCached(line Line) {
	hash := func(obj interface{}) [16]byte {
		bytes, _ := json.Marshal(obj)
		return md5.Sum(bytes)
	}

	h := hash(line)
	if pts, ok := pointCache[h]; ok {
		for _, pt := range pts {
			a.points = append(a.points, pt)
		}
		return
	}

	left, right := minmax(line.X1, line.X2)
	top, bottom := minmax(line.Y1, line.Y2)
	dx := right - left
	dy := line.Y2 - line.Y1
	if dx == 0 {
		for y := top; y <= bottom; y++ {
			a.points = append(a.points, Point{left, y})
		}
	} else if dy == 0 {
		for x := left; x <= right; x++ {
			a.points = append(a.points, Point{x, top})
		}
	}

	// be sure to add these to the cache
	pointCache[h] = a.points
	fmt.Println("generated", len(a.points), "points")
}

func (a vectorToRasterAdapter) GetPoints() []Point {
	return a.points
}

func VectorToRaster(vi *VectorImage) RasterImage {
	adapter := vectorToRasterAdapter{}
	for _, line := range vi.Lines {
		adapter.addLineCached(line)
	}
	return adapter // as RasterImage
}

func main() {
	rc := NewRectangle(6, 4)
	a := VectorToRaster(rc) // adapter!
	_ = VectorToRaster(rc) // adapter!
	fmt.Print(DrawPoints(a))
}
```

# Applicability

* Use the Adapter class when you want to use some existing class, but its interface isn't compatible with the rest of your code.
* Use the pattern when you want to reuse several existing subclasses that lack some common functionality that can't be added to the superclass.

# Pros and Cons

| Pros |Cons  |
|--|--|
| _Single Responsibility Principle_. You can separate the interface or data conversion code from the primary business logic of the program. | The overall complexity of the code increases because you need to introduce a set of new interfaces and classes. Sometimes it’s simpler just to change the service class so that it matches the rest of your code. |
| _Open/Closed Principle_. You can introduce new types of adapters into the program without breaking the existing client code, as long as they work with the adapters through the client interface.|
