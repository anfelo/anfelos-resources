# Motivation

-   Avoid redundancy when storing data
-   E.g., MMORPG
    -   Plenty of users with identical first/last names
    -   No sense in storing same first/last name over and over again
    -   Store a list of names and references to them (indices, pointers, ect)
-   E.g., bold or italic text formatting
    -   Don't want each character to have a formatting character
    -   Operate on ranges (e.g, line number, start/end positions)

# Flyweight

A space optimization technique that lets us use less memory by storing externally the data associated with similar objects.

# Problem

You need to build a mobile game that lets users create their own avatar and give it a name, a 3D model between a Human, an Orc and a Goblin, and a special dance move. Your game is trending and millions of players have created their avatars. After a while you start to receive crash reports on the users' phones. When you start debugging you find out that the game allows users to add friends and those are stored in the device storage with all their customizations, the names, the 3D models and dance moves. 

# Solution

You can solve the problem by storing only a reference of each of the reusable properties of the avatars. For example the 3D model. You just have 3 that they can choose from, so you do no need to store the entire 3D model on each avatar. You could just store a pointer to the 3D model. Same with the names. You could keep a names storage, not all the names are unique, most of those are repeated. At the end you end up with a very light weight version of your avatar with a bunch of pointers to objects that can be reused from avatar to avatar.

# Code Example

```go
package main

import (
"fmt"
"strings"
)

type User struct {
FullName string
}

func NewUser(fullName string) *User {
return &User{FullName: fullName}
}

var allNames []string

type User2 struct {
names []uint8
}

func NewUser2(fullName string) *User2 {
	getOrAdd := func(s string) uint8 {
		for i := range allNames {
			if allNames[i] == s {
				return uint8(i)
			}
		}
		allNames = append(allNames, s)
		return uint8(len(allNames) - 1)
	}
	result := User2{}
	parts := strings.Split(fullName, " ")
	for _, p := range parts {
		result.names = append(result.names, getOrAdd(p))
	}
	return &result
}

func (u *User2) FullName() string {
	var parts []string
	for _, id := range u.names {
		parts = append(parts, allNames[id])
	}
	return strings.Join(parts, " ")
}

func main() {
	john := NewUser("John Doe")
	jane := NewUser("Jane Doe")
	alsoJane := NewUser("Jane Smith")
	fmt.Println(john.FullName)
	fmt.Println(jane.FullName)
	fmt.Println("Memory taken by users:",
		len([]byte(john.FullName))+
		len([]byte(alsoJane.FullName))+
		len([]byte(jane.FullName)))
	john2 := NewUser2("John Doe")
	jane2 := NewUser2("Jane Doe")
	alsoJane2 := NewUser2("Jane Smith")
	fmt.Println(john2.FullName())
	fmt.Println(jane2.FullName())
	
	totalMem := 0
	for _, a := range allNames {
		totalMem += len([]byte(a))
	}
	totalMem += len(john2.names)
	totalMem += len(jane2.names)
	totalMem += len(alsoJane2.names)
	fmt.Println("Memory taken by users2:", totalMem)
}
```

# Applicability

* Use the Flyweight pattern only when your program must support a huge number of objects which barely fit into available RAM.
* The benefit of applying the pattern depends heavily on how and where it’s used. It’s most useful when:
	-   an application needs to spawn a huge number of similar objects
	-   this drains all available RAM on a target device
	-   the objects contain duplicate states which can be extracted and shared between multiple objects

# Pros and Cons

|Pros| Cons |
|--|--|
| You can save lots of RAM, assuming your program has tons of similar objects. | You might be trading RAM over CPU cycles when some of the context data needs to be recalculated each time somebody calls a flyweight method. |
|  | The code becomes much more complicated. New team members will always be wondering why the state of an entity was separated in such a way. |
