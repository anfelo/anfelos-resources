# Motivation

- Unethical behavior by an employee; who takes the blame?
	- Employee
	- Manager
	- CEO
- You click a graphical element on a form
	- Button handles it, stops further processing
	- Underlying group box
- CCG computer game

# Chain of Responsibility

A chain of components who all get a chance to process a command or query, optionally having default processing implementation and ability to terminate the processing chain.
Upon receiving a request, each handler decides either to process the request or to pass it to the next handler in the chain.

## Command Query Separation
  
- Command = asking for an action or change (e.g., please set your attack value to 2).
- Query = asking for information (e.g., please give me your attack value)
- CQS = having separate means of sending commands and queries to e.g., direct field access

# Problem

There is always some task that your system has to perform but before it does, many conditions and preparations have to occur before the system can execute the task. For example, the user has to be authenticated, the user then has to have admin permissions, the data has to be cleaned and sanitized... The system starts to get bloated and complex.

# Solution

You could design your system to have a bunch of similar objects that behave in the same way, "handlers". These objects with have a method to perform some action in the incoming request and they know which handler is the next in line by holding a reference to it.
Finally, a handler can decide to break the chain if something is not looking good.
 
 It’s crucial that all handler classes implement the same interface. Each concrete handler should only care about the following one having the `execute` method. This way you can compose chains at runtime, using various handlers without coupling your code to their concrete classes.

# Code Example

```go
package main

import "fmt"

type Creature struct {
	Name string
	Attack, Defense int
}

func (c *Creature) String() string {
	return fmt.Sprintf("%s (%d/%d)", c.Name, c.Attack, c.Defense)
}

func NewCreature(name string, attack int, defense int) *Creature {
	return  &Creature{Name: name, Attack: attack, Defense: defense}
}

type Modifier interface {
	Add(m Modifier)
	Handle()
}

type CreatureModifier struct {
	creature *Creature
	next Modifier // singly linked list
}

func (c *CreatureModifier) Add(m Modifier) {
	if c.next !=  nil {
		c.next.Add(m)
	} else {
		c.next = m
	}
}

func (c *CreatureModifier) Handle() {
	if c.next !=  nil {
		c.next.Handle()
	}
}

func NewCreatureModifier(creature *Creature) *CreatureModifier {
	return  &CreatureModifier{creature: creature}
}

type DoubleAttackModifier struct {
	CreatureModifier
}

func NewDoubleAttackModifier(c *Creature) *DoubleAttackModifier {
	return  &DoubleAttackModifier{CreatureModifier{creature: c}}
}

type IncreasedDefenseModifier struct {
	CreatureModifier
}

func NewIncreasedDefenseModifier(c *Creature) *IncreasedDefenseModifier {
	return &IncreasedDefenseModifier{CreatureModifier{creature: c}}
}

func (i *IncreasedDefenseModifier) Handle() {
	if i.creature.Attack <=  2 {
		fmt.Println("Increasing",
		i.creature.Name, "\b's defense")
		i.creature.Defense++
	}
	i.CreatureModifier.Handle()
}

func (d *DoubleAttackModifier) Handle() {
	fmt.Println("Doubling", d.creature.Name, "attack...")
	d.creature.Attack *=  2
	d.CreatureModifier.Handle()
}

type NoBonusesModifier struct {
	CreatureModifier
}

func NewNoBonusesModifier(c *Creature) *NoBonusesModifier {
	return  &NoBonusesModifier{CreatureModifier{creature: c}}
}

func (n *NoBonusesModifier) Handle() {
	// nothing here!
}

func main() {
	goblin :=  NewCreature("Goblin", 1, 1)
	fmt.Println(goblin.String())
	root :=  NewCreatureModifier(goblin)
	//root.Add(NewNoBonusesModifier(goblin))
	root.Add(NewDoubleAttackModifier(goblin))
	root.Add(NewIncreasedDefenseModifier(goblin))
	root.Add(NewDoubleAttackModifier(goblin))
	// eventually process the entire chain
	root.Handle()
	fmt.Println(goblin.String())
}
```

# Applicability

* Use the Chain of Responsibility pattern when your program is expected to process different kinds of requests in various ways, but the exact types of requests and their sequences are unknown beforehand.
* Use the pattern when it’s essential to execute several handlers in a particular order.
* Use the CoR pattern when the set of handlers and their order are supposed to change at runtime.

# Pros and Cons

| Pros | Cons |
|--|--|
| You can control the order of request handling. | Some requests may end up unhandled. |
| _Single Responsibility Principle_. You can decouple classes that invoke operations from classes that perform operations. |  |
| _Open/Closed Principle_. You can introduce new handlers into the app without breaking the existing client code. |  |
