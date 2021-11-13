# Motivation

-   Consider an ordinary telephone
-   What you do whti it depends on the state of the phone/line
    -   If it's ringing or you want to make a call, you can pick it up
    -   Phone must be off the hook to talk/make a call
    -   If you try calling someone, and it's busy, you put the handset down
-   Changes in state can be explicit or in response to event (Observer pattern)

# State

A pattern in which the object's behavior is determined by its state. An object transitions from one state to another (something needs to trigger a transition).

A formalized construct which manages state and transitions is called a state machine.

# Problem

UI components regularly have different looks and behaviors depending on the user interaction. The user might be passing the mouse over the component, or maybe they clicked the component. The component can have a state management system based on this conditions but this is not sustainable. As more and more interactions are added and new state needs to be processed, makes the component look complicated because of all the conditional checks.

# Solution

The state pattern suggests the we create a class for each state of the component. These classes will be in charge of containing the behaviors in that particular state. Now the component can store 1 and only 1 state object as the current state of the object. When some interaction occurs, the current state object may trigger a change in state and the component will react to that change accordingly.

# Code Examples

## Classic State

```go
package main

import "fmt"

type Switch struct {
	State State
}

func NewSwitch() *Switch {
	return &Switch{NewOffState()}
}

func (s *Switch) On() {
	s.State.On(s)
}

func (s *Switch) Off() {
	s.State.Off(s)
}

type State interface {
	On(sw *Switch)
	Off(sw *Switch)
}

type BaseState struct{}

func (s *BaseState) On(sw *Switch) {
	fmt.Println("Light is already on")
}

func (s *BaseState) Off(sw *Switch) {
	fmt.Println("Light is already off")
}

type OnState struct {
	BaseState
}

func NewOnState() *OnState {
	fmt.Println("Light turned on")
	return &OnState{BaseState{}}
}

func (o *OnState) Off(sw *Switch) {
	fmt.Println("Turning light off...")
	sw.State = NewOffState()
}

type OffState struct {
	BaseState
}

func NewOffState() *OffState {
	fmt.Println("Light turned off")
	return &OffState{BaseState{}}
}

func (o *OffState) On(sw *Switch) {
	fmt.Println("Turning light on...")
	sw.State = NewOnState()
}

func main() {
	sw := NewSwitch()
	sw.On()
	sw.Off()
	sw.Off()
}
```

## State Machine

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
)

type State int

const (
	OffHook State = iota
	Connecting
	Connected
	OnHold
	OnHook
)

func (s State) String() string {
	switch s {
	case OffHook:
		return "OffHook"
	case Connecting:
		return "Connecting"
	case Connected:
		return "Connected"
	case OnHold:
		return "OnHold"
	case OnHook:
		return "OnHook"
	}
	return "Unknown"
}

type Trigger int

const (
	CallDialed Trigger = iota
	HungUp
	CallConnected
	PlacedOnHold
	TakenOffHold
	LeftMessage
)

func (t Trigger) String() string {
	switch t {
	case CallDialed:
		return "CallDialed"
	case HungUp:
		return "HungUp"
	case CallConnected:
		return "CallConnected"
	case PlacedOnHold:
		return "PlacedOnHold"
	case TakenOffHold:
		return "TakenOffHold"
	case LeftMessage:
		return "LeftMessage"
	}
	return "Unknown"
}

type TriggerResult struct {
	Trigger Trigger
	State State
}

var rules = map[State][]TriggerResult{
	OffHook: {
		{CallDialed, Connecting},
	},
	Connecting: {
		{HungUp, OffHook},
		{CallConnected, Connected},
	},
	Connected: {
		{LeftMessage, OnHook},
		{HungUp, OnHook},
		{PlacedOnHold, OnHold},
	},
	OnHold: {
		{TakenOffHold, Connected},
		{HungUp, OnHook},
	},
}

func main() {
	state, exitState := OffHook, OnHook
	for ok := true; ok; ok = state != exitState {
		fmt.Println("The phone is currently", state)
		fmt.Println("Select a trigger:")
		for i := 0; i < len(rules[state]); i++ {
			tr := rules[state][i]
			fmt.Println(strconv.Itoa(i), ".", tr.Trigger)
		}
		input, _, _ := bufio.NewReader(os.Stdin).ReadLine()
		i, _ := strconv.Atoi(string(input))
		tr := rules[state][i]
		state = tr.State
	}
	fmt.Println("We are done using the phone")
}
```

# Applicability

* Use the State pattern when you have an object that behaves differently depending on its current state, the number of states is enormous, and the state-specific code changes frequently.
* Use the pattern when you have a class polluted with massive conditionals that alter how the class behaves according to the current values of the class’s fields.
* Use State when you have a lot of duplicate code across similar states and transitions of a condition-based state machine.

# Pros and Cons

| Pros | Cons |
|--|--|
| _Single Responsibility Principle_. Organize the code related to particular states into separate classes. | Applying the pattern can be overkill if a state machine has only a few states or rarely changes. |
| _Open/Closed Principle_. Introduce new states without changing existing state classes or the context. |  |
| Simplify the code of the context by eliminating bulky state machine conditionals. |  |
