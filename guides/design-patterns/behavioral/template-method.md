# Motivation

-   Algorithms can be decomposed into common parts + specifics
-   Strategy pattern does this through composition
    -   High-level algorithm uses an interface
    -   Concrete implementations implement the interface
    -   We keep a pointer to the interface; provide concrete implementations
-   Template Method performs a similar operation, but
    -   It's typically just a function, not a struct with a reference to the implementation
    -   Can still use interfaces (just like Strategy); or
    -   Can be functional (take several functions as parameters)

# Template Method

A skeleton algorithm defined in a function. Function can be either use an interface (like Strategy) or can take several functions as arguments.

# Problem

This pattern is well suited when we have various processes that have many steps in common but vary in 1 or two behaviors. For example a data mining algorithm that can read .doc files as well as .csv files. The process in both cases is similar, it only differs in the way data is extracted from the file and the parsing of the data. Other than that is the same, analyzing, sending a report opening and closing the file.

# Solution

 The Template Method pattern suggests that you break down an algorithm into a series of steps, turn these steps into methods, and put a series of calls to these methods inside a single _template method._ The steps may either be `abstract`, or have some default implementation. To use the algorithm, the client is supposed to provide its own subclass, implement all abstract steps, and override some of the optional ones if needed (but not the template method itself).

# Code Example

```go
package main

import "fmt"

type Game interface {
	Start()
	HaveWinner() bool
	TakeTurn()
	WinningPlayer() int
}

func PlayGame(g Game) {
	g.Start()
	for !g.HaveWinner() {
		g.TakeTurn()
	}
	fmt.Printf("Player %d wins.\n", g.WinningPlayer())
}

type chess struct {
	turn, maxTurns, currentPlayer int
}

func NewGameOfChess() Game {
	return &chess{1, 10, 0}
}

func (c *chess) Start() {
	fmt.Println("Starting a game of chess.")
}

func (c *chess) HaveWinner() bool {
	return c.turn == c.maxTurns
}

func (c *chess) TakeTurn() {
	c.turn++
	fmt.Printf("Turn %d taken by player %d\n",
	c.turn, c.currentPlayer)
	c.currentPlayer = (c.currentPlayer + 1) % 2
}

func (c *chess) WinningPlayer() int {
	return c.currentPlayer
}

func main() {
	chess := NewGameOfChess()
	PlayGame(chess)
}
```

## Functional Template Method

```go
package main

import "fmt"

func PlayGame(
	start,
	takeTurn func(),
	haveWinner func() bool,
	winningPlayer func() int,
) {
	start()
	for !haveWinner() {
		takeTurn()
	}
	fmt.Printf("Player %d wins.\n", winningPlayer())
}

func main() {
	turn, maxTurns, currentPlayer := 1, 10, 0
	start := func() {
		fmt.Println("Starting a game of chess.")
	}
	takeTurn := func() {
		turn++
		fmt.Printf("Turn %d taken by player %d\n",
		turn, currentPlayer)
		currentPlayer = (currentPlayer + 1) % 2
	}
	haveWinner := func() bool {
		return turn == maxTurns
	}
	winningPlayer := func() int {
		return currentPlayer
	}
	PlayGame(start, takeTurn, haveWinner, winningPlayer)
}
```

# Applicability

* Use the Template Method pattern when you want to let clients extend only particular steps of an algorithm, but not the whole algorithm or its structure.
* Use the pattern when you have several classes that contain almost identical algorithms with some minor differences. As a result, you might need to modify all classes when the algorithm changes.

# Pros and Cons

| Pros | Cons |
|--|--|
| You can let clients override only certain parts of a large algorithm, making them less affected by changes that happen to other parts of the algorithm. | Some clients may be limited by the provided skeleton of an algorithm. |
| You can pull the duplicate code into a superclass. | You might violate the _Liskov Substitution Principle_ by suppressing a default step implementation via a subclass. |
|  | Template methods tend to be harder to maintain the more steps they have. |
