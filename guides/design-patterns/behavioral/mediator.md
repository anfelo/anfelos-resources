# Motivation

-   Components may go in and out of a system at any time
    -   Chat room participants
    -   Players in an MMORPG
-   It makes no sense for them to have direct references (pointers) to one another
    -   Those references may go dead
-   Solution: have then all refer to some central component that facilitates communication

# Mediator

A component that facilitates communication between other components without them necessarily being aware of each other or having direct (reference) access to each other.

# Problem

Handling communication between classes or components starts to get difficult when there is not a central piece that uncouples them. For example, having a chat application, new users will have to hold a reference to all the existing users in the chat just to send a message to all of them. What if one of the users leaves the chat? 

# Solution

Comes the Mediator pattern to the rescue. The idea is prevent direct communication between classes and components that you need to make independent from each other. Make them collaborate indirectly by calling a mediator object that will redirect the calls and orchestrate the communications. 

In the example of the chat application. We can introduce the concept of a chat room to serve as a mediator. Participants join a chat room and leave at will, and when they want to speak to other participants, they send the message to the chat room and all other participants will be notified.

# Code Example

```go
package mediator

import "fmt"

type Person struct {
	Name string
	Room *ChatRoom
	chatLog []string
}

func NewPerson(name string) *Person {
	return &Person{Name: name}
}

func (p *Person) Receive(sender, message string) {
	s := fmt.Sprintf("%s: '%s'", sender, message)
	fmt.Printf("[%s's chat session] %s\n", p.Name, s)
	p.chatLog = append(p.chatLog, s)
}

func (p *Person) Say(message string) {
	p.Room.Broadcast(p.Name, message)
}

func (p *Person) PrivateMessage(who, message string) {
	p.Room.Message(p.Name, who, message)
}

type ChatRoom struct {
	people []*Person
}

func (c *ChatRoom) Broadcast(source, message string) {
	for _, p := range c.people {
		if p.Name != source {
			p.Receive(source, message)
		}
	}
}

func (c *ChatRoom) Join(p *Person) {
	joinMsg := p.Name + " joins the chat"
	c.Broadcast("Room", joinMsg)
	p.Room = c
	c.people = append(c.people, p)
}

func (c *ChatRoom) Message(src, dst, msg string) {
	for _, p := range c.people {
		if p.Name == dst {
			p.Receive(src, msg)
		}
	}
}

func main() {
	room := ChatRoom{}
	john := NewPerson("John")
	jane := NewPerson("Jane")
	room.Join(john)
	room.Join(jane)
	john.Say("hi room")
	jane.Say("oh, hey john")
	simon := NewPerson("Simon")
	room.Join(simon)
	simon.Say("hi everyone!")
	jane.PrivateMessage("Simon", "glad you could join us!")
}
```

# Applicability

* Use the Mediator pattern when it’s hard to change some of the classes because they are tightly coupled to a bunch of other classes.
* Use the pattern when you can’t reuse a component in a different program because it’s too dependent on other components.
* Use the Mediator when you find yourself creating tons of component subclasses just to reuse some basic behavior in various contexts.

# Pros and Cons

| Pros | Cons |
|--|--|
| _Single Responsibility Principle_. You can extract the communications between various components into a single place, making it easier to comprehend and maintain. | Over time a mediator can evolve into a [God Object](https://en.wikipedia.org/wiki/God_object). |
| _Open/Closed Principle_. You can introduce new mediators without having to change the actual components. |  |
| You can reduce coupling between various components of a program. |  |
| You can reuse individual components more easily. |  |

