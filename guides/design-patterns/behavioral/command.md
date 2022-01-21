# Motivation

-   Ordinary statements are perishable
    -   Cannot undo field assignment
    -   Cannot directly serialize a sequence of actions (calls)
-   Want an object that represents an operation
    -   person should change its age to value 22
    -   car should do explode()
-   Uses: GUI commands, multi-level undo/redo, macro recording and more!

# Command

An object which represents an instruction to perform a particular action. Contains all the information necessary for the action to be taken. The actual processing of the command can be handle by the command or by some other command processor.

# Problem

There are two problems that can explain why we need the command pattern. The first is when we have many subclasses of some component and they have different behaviors. The second case is when we need a mechanism to undo or redo operations.

# Solution

The solution is to extract all of the request details, such as the object being called, the name of the method and the list of arguments into a separate _command_ class with a single method that triggers this request.

The command class implement a common interface where there is one method to handle or execute the command. The command will be configured with the necessary parameters to proceed with the execution.

# Code Examples

## Command

```go
package main

import "fmt"

var overdraftLimit = -500

type BankAccount struct {
	balance int
}

func (b *BankAccount) Deposit(amount int) {
	b.balance += amount
	fmt.Println("Deposited", amount, "\b, balance is now", b.balance)
}

func (b *BankAccount) Withdraw(amount int) bool {
	if b.balance-amount >= overdraftLimit {
		b.balance -= amount
		fmt.Println("Withdrew", amount, "\b, balance is now", b.balance)
		return true
	}
	return false
}

type Command interface {
	Call()
	Undo()
}

type Action int

const (
	Deposit Action = iota
	Withdraw
)

type BankAccountCommand struct {
	account *BankAccount
	action Action
	amount int
	succeeded bool
}

func (b *BankAccountCommand) Call() {
	switch b.action {
	case Deposit:
		b.account.Deposit(b.amount)
		b.succeeded = true
	case Withdraw:
		b.succeeded = b.account.Withdraw(b.amount)
	}
}

func (b *BankAccountCommand) Undo() {
	if !b.succeeded {
		return
	}
	switch b.action {
	case Deposit:
		b.account.Withdraw(b.amount)
	case Withdraw:
		b.account.Deposit(b.amount)
	}
}

func NewBankAccountCommand(account *BankAccount, action Action, amount int) *BankAccountCommand {
	return &BankAccountCommand{account: account, action: action, amount: amount}
}

func main() {
	ba := BankAccount{}
	cmd := NewBankAccountCommand(&ba, Deposit, 100)
	cmd.Call()
	cmd2 := NewBankAccountCommand(&ba, Withdraw, 50)
	cmd2.Call()
	fmt.Println(ba)
}
```

## Composite Command

```go
package main

import "fmt"

var overdraftLimit = -500

type BankAccount struct {
	balance int
}

func (b *BankAccount) Deposit(amount int) {
	b.balance += amount
	fmt.Println("Deposited", amount, "\b, balance is now", b.balance)
}

func (b *BankAccount) Withdraw(amount int) bool {
	if b.balance-amount >= overdraftLimit {
		b.balance -= amount
		fmt.Println("Withdrew", amount, "\b, balance is now", b.balance)
		return true
	}
	return false
}

type Command interface {
	Call()
	Undo()
	Succeeded() bool
	SetSucceeded(value bool)
}

type Action int

const (
	Deposit Action = iota
	Withdraw
)

type BankAccountCommand struct {
	account *BankAccount
	action Action
	amount int
	succeeded bool
}

func (b *BankAccountCommand) SetSucceeded(value bool) {
	b.succeeded = value
}

// additional member
func (b *BankAccountCommand) Succeeded() bool {
	return b.succeeded
}

func (b *BankAccountCommand) Call() {
	switch b.action {
	case Deposit:
		b.account.Deposit(b.amount)
		b.succeeded = true
	case Withdraw:
		b.succeeded = b.account.Withdraw(b.amount)
	}
}

func (b *BankAccountCommand) Undo() {
	if !b.succeeded {
		return
	}
	switch b.action {
	case Deposit:
		b.account.Withdraw(b.amount)
	case Withdraw:
		b.account.Deposit(b.amount)
	}
}

type CompositeBankAccountCommand struct {
	commands []Command
}

func (c *CompositeBankAccountCommand) Succeeded() bool {
	for _, cmd := range c.commands {
		if !cmd.Succeeded() {
			return false
		}
	}
	return true
}

func (c *CompositeBankAccountCommand) SetSucceeded(value bool) {
	for _, cmd := range c.commands {
		cmd.SetSucceeded(value)
	}
}

func (c *CompositeBankAccountCommand) Call() {
	for _, cmd := range c.commands {
		cmd.Call()
	}
}

func (c *CompositeBankAccountCommand) Undo() {
	// undo in reverse order
	for idx := range c.commands {
		c.commands[len(c.commands)-idx-1].Undo()
	}
}

func NewBankAccountCommand(account *BankAccount, action Action, amount int) *BankAccountCommand {
	return &BankAccountCommand{account: account, action: action, amount: amount}
}

type MoneyTransferCommand struct {
	CompositeBankAccountCommand
	from, to *BankAccount
	amount int
}

func NewMoneyTransferCommand(from *BankAccount, to *BankAccount, amount int) *MoneyTransferCommand {
	c := &MoneyTransferCommand{from: from, to: to, amount: amount}
	c.commands = append(c.commands,
	NewBankAccountCommand(from, Withdraw, amount))
	c.commands = append(c.commands,
	NewBankAccountCommand(to, Deposit, amount))
	return c
}

func (m *MoneyTransferCommand) Call() {
	ok := true
	for _, cmd := range m.commands {
		if ok {
			cmd.Call()
			ok = cmd.Succeeded()
		} else {
			cmd.SetSucceeded(false)
		}
	}
}

func main() {
	ba := &BankAccount{}
	cmdDeposit := NewBankAccountCommand(ba, Deposit, 100)
	cmdWithdraw := NewBankAccountCommand(ba, Withdraw, 1000)
	cmdDeposit.Call()
	cmdWithdraw.Call()
	fmt.Println(ba)
	cmdWithdraw.Undo()
	cmdDeposit.Undo()
	fmt.Println(ba)
	from := BankAccount{100}
	to := BankAccount{0}
	mtc := NewMoneyTransferCommand(&from, &to, 100) // try 1000
	mtc.Call()
	fmt.Println("from=", from, "to=", to)
	fmt.Println("Undoing...")
	mtc.Undo()
	fmt.Println("from=", from, "to=", to)
}
```

## Functional Command

```go
package main

import "fmt"

type BankAccount struct {
	Balance int
}

func Deposit(ba *BankAccount, amount int) {
	fmt.Println("Depositing", amount)
	ba.Balance += amount
}

func Withdraw(ba *BankAccount, amount int) {
	if ba.Balance >= amount {
		fmt.Println("Withdrawing", amount)
		ba.Balance -= amount
	}
}

func main() {
	ba := &BankAccount{0}
	var commands []func()
	commands = append(commands, func() {
		Deposit(ba, 100)
	})
	commands = append(commands, func() {
		Withdraw(ba, 100)
	})
	for _, cmd := range commands {
		cmd()
	}
}
```

# Applicability

* Use the Command pattern when you want to parameterize objects with operations.
* Use the Command pattern when you want to queue operations, schedule their execution, or execute them remotely.
* Use the Command pattern when you want to implement reversible operations.

# Pros and Cons

| Pros | Cons |
|--|--|
| _Single Responsibility Principle_. You can decouple classes that invoke operations from classes that perform these operations. | The code may become more complicated since you’re introducing a whole new layer between senders and receivers. |
| _Open/Closed Principle_. You can introduce new commands into the app without breaking existing client code. |  |
| You can implement undo/redo. |  |
| You can implement deferred execution of operations. |  |
| You can assemble a set of simple commands into a complex one. |  |
