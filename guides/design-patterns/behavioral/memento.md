# Motivation

-   An object or system goes through changes
    -   E.g., a bank gets deposits and withdrawals
-   There are different ways of navigating these changes
-   One way is to record every change (Command) and teach a command to 'undo' itself
    -   Also part of CQRS = Command Query Responsibility Segregation
-   Another is to simply save snapshots of the system

# Memento

A token representing the system state. Lets us roll back to the state when the token was generated. May or may not directly expose state information.

**Memento**  is a behavioral design pattern that lets you save and restore the previous state of an object without revealing the details of its implementation.

## Memento vs Flyweight

-   Both patterns provide a 'token' clients can hold on to
-   Memento is used only to be fed back into the system
    -   No public/mutable state
    -   No methods
-   A flyweight is similar to an ordinary reference to object
    -   Can mutate state
    -   Can provide additional functionality (fields/methods)

# Problem

Sometimes you have an app that needs to rollback to a previous state (e.g. text editor). For this you need to store a snapshot every time something changes in the state. This is quite difficult and can crash the memory, depending on how you store the snapshots and how often.

# Solution

The solution to this problem is to make all the classes that need to store their state be able to create a snapshot of themselves. And store all the snapshots in a memento object that will protect all the snapshots from other classes. The memento will expose a simple API for fetching snapshots' metadata.

# Code Example

```go
package memento

import (
	"fmt"
)

type Memento struct {
	Balance int
}

type BankAccount struct {
	balance int
}

func (b *BankAccount) Deposit(amount int) *Memento {
	b.balance += amount
	return &Memento{b.balance}
}

func (b *BankAccount) Restore(m *Memento) {
	b.balance = m.Balance
}

func main() {
	ba := BankAccount{100}
	m1 := ba.Deposit(50)
	m2 := ba.Deposit(25)
	fmt.Println(ba)
	ba.Restore(m1)
	fmt.Println(ba) // 150
	ba.Restore(m2)
	fmt.Println(ba)
}
```

## Undo Redo

```go
package memento

import "fmt"

type Memento struct {
	Balance int
}

type BankAccount struct {
	balance int
	changes []*Memento
	current int
}

func (b *BankAccount) String() string {
	return fmt.Sprint("Balance = $", b.balance, ", current = ", b.current)
}

func NewBankAccount(balance int) *BankAccount {
	b := &BankAccount{balance: balance}
	b.changes = append(b.changes, &Memento{balance})
	return b
}

func (b *BankAccount) Deposit(amount int) *Memento {
	b.balance += amount
	m := Memento{b.balance}
	b.changes = append(b.changes, &m)
	b.current++
	fmt.Println("Deposited", amount, ", balance is now", b.balance)
	return &m
}

func (b *BankAccount) Restore(m *Memento) {
	if m != nil {
		b.balance -= m.Balance
		b.changes = append(b.changes, m)
		b.current = len(b.changes) - 1
	}
}

func (b *BankAccount) Undo() *Memento {
	if b.current > 0 {
		b.current--
		m := b.changes[b.current]
		b.balance = m.Balance
		return m
	}
	return nil // nothing to undo
}

func (b *BankAccount) Redo() *Memento {
	if b.current+1 < len(b.changes) {
		b.current++
		m := b.changes[b.current]
		b.balance = m.Balance
		return m
	}
	return nil
}

func main() {
	ba := NewBankAccount(100)
	ba.Deposit(50)
	ba.Deposit(25)
	fmt.Println(ba)
	ba.Undo()
	fmt.Println("Undo 1:", ba)
	ba.Undo()
	fmt.Println("Undo 2:", ba)
	ba.Redo()
	fmt.Println("Redo:", ba)
}
```

# Applicability

* Use the Memento pattern when you want to produce snapshots of the object’s state to be able to restore a previous state of the object.
* Use the pattern when direct access to the object’s fields/getters/setters violates its encapsulation.

# Pros and Cons

| Pros | Cons |
|--|--|
| You can produce snapshots of the object’s state without violating its encapsulation. | The app might consume lots of RAM if clients create mementos too often. |
| You can simplify the originator’s code by letting the caretaker maintain the history of the originator’s state. | Caretakers should track the originator’s lifecycle to be able to destroy obsolete mementos.|
|  | Most dynamic programming languages, such as PHP, Python and JavaScript, can’t guarantee that the state within the memento stays untouched. |
