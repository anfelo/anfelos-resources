```ts
// The element interface declares an `accept` method that takes
// the base visitor interface as an argument.
interface Shape {
    method move(x, y)
    method draw()
    method accept(v: Visitor)
}

// Each concrete element class must implement the `accept`
// method in such a way that it calls the visitor's method that
// corresponds to the element's class.
class Dot implements Shape {
    // ...

    // Note that we're calling `visitDot`, which matches the
    // current class name. This way we let the visitor know the
    // class of the element it works with.
    method accept(v: Visitor) {
        v.visitDot(this)
    }
}

class Circle implements Shape {
    // ...
    method accept(v: Visitor) {
        v.visitCircle(this)
    }
}

class Rectangle implements Shape {
    // ...
    method accept(v: Visitor) {
        v.visitRectangle(this)
    }
}

class CompoundShape implements Shape {
    // ...
    method accept(v: Visitor) {
        v.visitCompoundShape(this)
    }
}


// The Visitor interface declares a set of visiting methods that
// correspond to element classes. The signature of a visiting
// method lets the visitor identify the exact class of the
// element that it's dealing with.
interface Visitor {
    method visitDot(d: Dot)
    method visitCircle(c: Circle)
    method visitRectangle(r: Rectangle)
    method visitCompoundShape(cs: CompoundShape)
}

// Concrete visitors implement several versions of the same
// algorithm, which can work with all concrete element classes.
//
// You can experience the biggest benefit of the Visitor pattern
// when using it with a complex object structure such as a
// Composite tree. In this case, it might be helpful to store
// some intermediate state of the algorithm while executing the
// visitor's methods over various objects of the structure.
class XMLExportVisitor implements Visitor {
    method visitDot(d: Dot) {
        // Export the dot's ID and center coordinates.
    }

    method visitCircle(c: Circle) {
        // Export the circle's ID, center coordinates and
        // radius.
    }

    method visitRectangle(r: Rectangle) {
        // Export the rectangle's ID, left-top coordinates,
        // width and height.
    }

    method visitCompoundShape(cs: CompoundShape) {
        // Export the shape's ID as well as the list of its
        // children's IDs.
    }


// The client code can run visitor operations over any set of
// elements without figuring out their concrete classes. The
// accept operation directs a call to the appropriate operation
// in the visitor object.
class Application {
    field allShapes: array of Shapes

    method export() {
        exportVisitor = new XMLExportVisitor()

        foreach (shape in allShapes) do
            shape.accept(exportVisitor)
    }
}
```