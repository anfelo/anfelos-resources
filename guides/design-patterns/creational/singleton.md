# Motivation

-   For some components it only makes sense to have one in the system
    -   Database repository
    -   Object factory
-   E.g. the constructor call is expensive
    -   We only do it once
    -   We give everyone the same instance
-   Want to prevent anyone creating additional copies
-   Need to take care of lazy instantiation

# Singleton

A component which is instantiated only once. This is particularly useful if we want to load some expensive object into memory.

## Problems with singletons

It is difficult to mock singletons when testing and this makes it hard to test functionality. This is because we depend on the concrete implementation of the singletons. This goes against the Dependency Inversion Principle (DIP).

## Singleton and Dependency Inversion

A simple fix is to pass as a dependency the interface of what our singleton looks like to the method that is using it directly. That way, when we are going to use the method, we need to pass a class (e.g. database) that can perform the singletons functionality.

# Problem

One of the problems that the Singleton solves is when we only need one instance of an object in our entire system because it stores some shared resource (like a Database). 
A second problem that this pattern solves is if we want to access an object that has some useful functionality that is needed around the entire app. This pattern makes the object accessible and protected from possible overrides.

# Solution

All implementations of the Singleton have these two steps in common:

-   Make the default constructor private, to prevent other objects from using the  `new`  operator with the Singleton class.
-   Create a static creation method that acts as a constructor. Under the hood, this method calls the private constructor to create an object and saves it in a static field. All following calls to this method return the cached object.

If your code has access to the Singleton class, then it’s able to call the Singleton’s static method. So whenever that method is called, the same object is always returned.

# Code Example

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"path/filepath"
	"strconv"
	"sync"
)

// think of a module as a singleton

type Database interface {
	GetPopulation(name string) int
}

type singletonDatabase struct {
	capitals map[string]int
}

func (db *singletonDatabase) GetPopulation(name string) int {
	return db.capitals[name]
}

// both init and sync.Once are thread-safe
// but only sync.Once is lazy
var once sync.Once

var instance Database

// init() — we could, but it's not lazy
func readData(path string) (map[string]int, error) {
	ex, err := os.Executable()
	if err != nil {
		panic(err)
	}
	exPath := filepath.Dir(ex)
	file, err := os.Open(exPath + path)
	if err != nil {
		return nil, err
	}
	defer file.Close()
	scanner := bufio.NewScanner(file)
	scanner.Split(bufio.ScanLines)
	result := map[string]int{}
	for scanner.Scan() {
		k := scanner.Text()
		scanner.Scan()
		v, _ := strconv.Atoi(scanner.Text())
		result[k] = v
	}
	return result, nil
}

func GetSingletonDatabase() Database {
	once.Do(func() {
		db := singletonDatabase{}
		caps, err := readData(".\\capitals.txt")
		if err == nil {
			db.capitals = caps
		}
		instance = &db
	})
	return instance
}

func GetTotalPopulation(cities []string) int {
	result := 0
	for _, city := range cities {
		result += GetSingletonDatabase().GetPopulation(city)
	}
	return result
}

// Need to provide a database: This way we can provide a mock database
// and solve the dependency inversion problem
func GetTotalPopulationEx(db Database, cities []string) int {
	result := 0
	for _, city := range cities {
		result += db.GetPopulation(city)
	}
	return result
}

// For testing purposes
type DummyDatabase struct {
	dummyData map[string]int
}

func (d *DummyDatabase) GetPopulation(name string) int {
	if len(d.dummyData) == 0 {
		d.dummyData = map[string]int{
			"alpha": 1,
			"beta": 2,
			"gamma": 3,
		}
	}
	return d.dummyData[name]
}

func main() {
	db := GetSingletonDatabase()
	pop := db.GetPopulation("Seoul")
	fmt.Println("Pop of Seoul = ", pop)
	cities := []string{"Seoul", "Mexico City"}
	//tp := GetTotalPopulation(cities)
	tp := GetTotalPopulationEx(GetSingletonDatabase(), cities)
	ok := tp == (17500000 + 17400000) // testing on live data
	fmt.Println(ok)
	names := []string{"alpha", "gamma"} // expect 4
	tp = GetTotalPopulationEx(&DummyDatabase{}, names)
	ok = tp == 4
	fmt.Println(ok)
}
```

# Applicability
 
 * Use the Singleton pattern when a class in your program should have just a single instance available to all clients; for example, a single database object shared by different parts of the program.
 * Use the Singleton pattern when you need stricter control over global variables.

# Pros and Cons

| Pros | Cons |
|--|--|
| You can be sure that a class has only a single instance. | Violates the _Single Responsibility Principle_. The pattern solves two problems at the time. |
| You gain a global access point to that instance. | The Singleton pattern can mask bad design, for instance, when the components of the program know too much about each other. |
| The singleton object is initialized only when it’s requested for the first time. | The pattern requires special treatment in a multithreaded environment so that multiple threads won’t create a singleton object several times. |
|  | It may be difficult to unit test the client code of the Singleton because many test frameworks rely on inheritance when producing mock objects. Since the constructor of the singleton class is private and overriding static methods is impossible in most languages, you will need to think of a creative way to mock the singleton. Or just don’t write the tests. Or don’t use the Singleton pattern. |

