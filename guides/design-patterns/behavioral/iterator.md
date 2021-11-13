# Motivation

-   Iteration (traversal) is a core functionality of various data structures
-   An iterator is a type that facilitates the traversal
    -   Keeps a pointer to the current element
    -   Knows how to move to different element
-   Go allows iteration with range
    -   Built-in support in many objects (arrays, slices, etc)
    -   Can be supported in our own structs

# Iterator

**Iterator** is a behavioral design pattern that lets you traverse elements of a collection without exposing its underlying representation (list, stack, tree, etc.).

# Problem

There are many collection types and each of those store data in some structure. Some of these are simple one dimension lists others a more complex containing multiple levels of depth. At the end we would love to have an easy way to go through each of the elements in the data structure without repeating the items.

# Solution

The idea is to extract the traversal behavior of the data structure into a separate Interface called the Iterator. Then the data structure or class can implement the interface and provide a simple interface to traverse its items.

# Code Examples

## Iteration

```go
package main

import "fmt"

type Person struct {
	FirstName, MiddleName, LastName string
}

func (p *Person) Names() []string {
	return []string{p.FirstName, p.MiddleName, p.LastName}
}

func (p *Person) NamesGenerator() <-chan string {
	out := make(chan string)
	go func() {
		defer close(out)
		if len(p.MiddleName) > 0 {
			out <- p.MiddleName
		}
		out <- p.LastName
	}()
	return out
}

func main() {
	p := Person{"Alexander", "Graham", "Bell"}
	for _, name := range p.Names() {
		fmt.Println(name)
	}
}
```

## Tree Traversal

```go
package iterator

import "fmt"

type Node struct {
	Value int
	left, right, parent *Node
}

func NewNode(value int, left *Node, right *Node) *Node {
	n := &Node{Value: value, left: left, right: right}
	left.parent = n
	right.parent = n
	return n
}

func NewTerminalNode(value int) *Node {
	return &Node{Value: value}
}

type InOrderIterator struct {
	Current *Node
	root *Node
	returnedStart bool
}

func NewInOrderIterator(root *Node) *InOrderIterator {
	i := &InOrderIterator{
		Current: root,
		root: root,
		returnedStart: false,
	}
	// move to the leftmost element
	for i.Current.left != nil {
		i.Current = i.Current.left
	}
	return i
}

func (i *InOrderIterator) Reset() {
	i.Current = i.root
	i.returnedStart = false
}

func (i *InOrderIterator) MoveNext() bool {
	if i.Current == nil {
		return false
	}
	if !i.returnedStart {
		i.returnedStart = true
		return true // can use first element
	}
	if i.Current.right != nil {
		i.Current = i.Current.right
		for i.Current.left != nil {
			i.Current = i.Current.left
		}
		return true
	} else {
		p := i.Current.parent
		for p != nil && i.Current == p.right {
			i.Current = p
			p = p.parent
		}
		i.Current = p
		return i.Current != nil
	}
}

type BinaryTree struct {
	root *Node
}

func NewBinaryTree(root *Node) *BinaryTree {
	return &BinaryTree{root: root}
}

func (b *BinaryTree) InOrder() *InOrderIterator {
	return NewInOrderIterator(b.root)
}

func main() {
	// 1
	// / \
	// 2 3
	// in-order: 213
	// preorder: 123
	// postorder: 231
	root := NewNode(
		1,
		NewTerminalNode(2),
		NewTerminalNode(3),
	)
	it := NewInOrderIterator(root)
	for it.MoveNext() {
		fmt.Printf("%d,", it.Current.Value)
	}
	fmt.Println("\b")
	t := NewBinaryTree(root)
	for i := t.InOrder(); i.MoveNext(); {
		fmt.Printf("%d,", i.Current.Value)
	}
	fmt.Println("\b")
}
```

# Applicability

* Use the Iterator pattern when your collection has a complex data structure under the hood, but you want to hide its complexity from clients (either for convenience or security reasons).
* Use the pattern to reduce duplication of the traversal code across your app.
* Use the Iterator when you want your code to be able to traverse different data structures or when types of these structures are unknown beforehand.

# Pros and Cons

| Pros | Cons |
|--|--|
| _Single Responsibility Principle_. You can clean up the client code and the collections by extracting bulky traversal algorithms into separate classes. | Applying the pattern can be an overkill if your app only works with simple collections. |
| _Open/Closed Principle_. You can implement new types of collections and iterators and pass them to existing code without breaking anything. | Using an iterator may be less efficient than going through elements of some specialized collections directly. |
| You can iterate over the same collection in parallel because each iterator object contains its own iteration state. |  |
| For the same reason, you can delay an iteration and continue it when needed. |  |
