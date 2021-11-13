# Motivation

-   We need to be informed when certain things happen
    -   Object's field changes
    -   Object does something
    -   Some external event occurs
-   We want to listen to events and notified when they occur
-   Two participants: observable and observer

# Observer

An observer is an object that wishes to be informed about events happening in the system. The entity generating the events is an observable.

**Observer** is a behavioral design pattern that lets you define a subscription mechanism to notify multiple objects about any events that happen to the object they’re observing.

# Problem

Imagine that you are exited for a new video game that is coming soon. You decide to go every day to the store to check if the video game is there. Eventually some day you will get it but most of the trips are pointless. Another way is that the store could send an email to every body in town when the video game is in the store. This would also be problematic, most people are not gamers or are not interested on the new video game.

# Solution

The store can add a "Subscription" mechanism, the users that want to be notified of the updates in the new video game can subscribe. Whenever there are updates, the store takes the list of their subscribers and sends an email to them. At any point in time subscribers can subscribe or unsubscribe from the updates stream.

```go
package main

import (
	"container/list"
	"fmt"
)

type Observable struct {
	subs *list.List
}

func (o *Observable) Subscribe(x Observer) {
	o.subs.PushBack(x)
}

func (o *Observable) Unsubscribe(x Observer) {
	for z := o.subs.Front(); z != nil; z = z.Next() {
		if z.Value.(Observer) == x {
			o.subs.Remove(z)
		}
	}
}

func (o *Observable) Fire(data interface{}) {
	for z := o.subs.Front(); z != nil; z = z.Next() {
		z.Value.(Observer).Notify(data)
	}
}

type Observer interface {
	Notify(data interface{})
}

// whenever a person catches a cold,
// a doctor must be called
type Person struct {
	Observable
	Name string
}

func NewPerson(name string) *Person {
	return &Person{
		Observable: Observable{new(list.List)},
		Name: name,
	}
}

func (p *Person) CatchACold() {
	p.Fire(p.Name)
}

type DoctorService struct{}

func (d *DoctorService) Notify(data interface{}) {
	fmt.Printf("A doctor has been called for %s",
	data.(string))
}

func main() {
	p := NewPerson("Boris")
	ds := &DoctorService{}
	p.Subscribe(ds)
	// let's test it!
	p.CatchACold()
}
```

# Applicability

* Use the Observer pattern when changes to the state of one object may require changing other objects, and the actual set of objects is unknown beforehand or changes dynamically.
* Use the pattern when some objects in your app must observe others, but only for a limited time or in specific cases.

# Pros and Cons

| Pros | Cons |
|--|--|
| _Open/Closed Principle_. You can introduce new subscriber classes without having to change the publisher’s code (and vice versa if there’s a publisher interface). | Subscribers are notified in random order. |
| You can establish relations between objects at runtime. |  |
