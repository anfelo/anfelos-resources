# Motivation

-   You are calling foo.Bar()
-   This assumes that foo is in the same process as Bar()
-   What if, later on, you want to put all Foo-related operations into a separate process?
    -   Can you avoid changing your code?
-   Proxy to the rescue!
    -   Same interface, entirely different behavior
-   This is called a communication proxy
    -   Other types: logging, virtual, guarding, ...

# Proxy

**Proxy** is a structural design pattern that lets you provide a substitute or placeholder for another object. A proxy controls access to the original object, allowing you to perform something either before or after the request gets through to the original object.

A type that functions as an interface to a particular resource. That resource may be remote, expensive to construct, or may require logging or some other added functionality.

## Protection Proxy

This is an interface that wraps some other object to protect it from being used given certain conditions.

## Virtual Proxy

A virtual proxy is an object that when created it just returns a none complete or 'virtual' implementation of the object, when someone requires to use it then the object will be created in a lazy form. This helps to prevent high load tasks to start when they are not yet required.

## Proxy vs. Decorator

-   Proxy tries to provide an **identical** interface; decorator provides an **enhanced** interface
-   Decorator typically aggregates (or has pointer to) what it is decorating; proxy doesn't have to
-   Proxy might not even be working with a materialized object

# Problem

You might have a very complex class that needs to run some intensive processes but it doesn't need to be on all the time. If this class is on it keeps consuming a bunch of resources just by being initialized. 

Another problem is that you might have a service that requires a specific configuration object molded by some complex business logic. If you try to initialize this service with the wrong configuration it might not work as you expect it to.

# Solution

For the first problem we have what is called a **Virtual Proxy**. An object that wraps the expensive class and when initialized it returns an empty instance of the expensive class not initialized. When the class is needed to start some intensive task, the proxy calls the appropriate constructor method and runs the task. This way the class is only initialized when it is needed.

For the second problem we have the **Protection Proxy** which wraps some service and before it is initialized, the proxy checks the the init object is correct. 

# Code Example

## Protection Proxy

```go
package main

import "fmt"

type Driven interface {
	Drive()
}

type Car struct{}

func (c *Car) Drive() {
	fmt.Println("Car being driven")
}

type Driver struct {
	Age int
}

type CarProxy struct {
	car Car
	driver *Driver
}

func (c *CarProxy) Drive() {
	if c.driver.Age >= 16 {
		c.car.Drive()
	} else {
		fmt.Println("Driver too young")
	}
}

func NewCarProxy(driver *Driver) *CarProxy {
	return &CarProxy{Car{}, driver}
}

func main() {
	car := NewCarProxy(&Driver{12})
	car.Drive()
}
```

# Virtual Proxy

```go
package main

import "fmt"

type Image interface {
	Draw()
}

type Bitmap struct {
	filename string
}

func (b *Bitmap) Draw() {
	fmt.Println("Drawing image", b.filename)
}

func NewBitmap(filename string) *Bitmap {
	fmt.Println("Loading image from", filename)
	return &Bitmap{filename: filename}
}

func DrawImage(image Image) {
	fmt.Println("About to draw the image")
	image.Draw()
	fmt.Println("Done drawing the image")
}

type LazyBitmap struct {
	filename string
	bitmap *Bitmap
}

func (l *LazyBitmap) Draw() {
	if l.bitmap == nil {
		l.bitmap = NewBitmap(l.filename)
	}
	l.bitmap.Draw()
}

func NewLazyBitmap(filename string) *LazyBitmap {
	return &LazyBitmap{filename: filename}
}

func main() {
	//bmp := NewBitmap("demo.png")
	bmp := NewLazyBitmap("demo.png")
	DrawImage(bmp)
}
```

# Applicability

* Lazy initialization (virtual proxy). This is when you have a heavyweight service object that wastes system resources by being always up, even though you only need it from time to time.
* Access control (protection proxy). This is when you want only specific clients to be able to use the service object; for instance, when your objects are crucial parts of an operating system and clients are various launched applications (including malicious ones).
* Local execution of a remote service (remote proxy). This is when the service object is located on a remote server.
* Logging requests (logging proxy). This is when you want to keep a history of requests to the service object.
* Caching request results (caching proxy). This is when you need to cache results of client requests and manage the life cycle of this cache, especially if results are quite large.
* Smart reference. This is when you need to be able to dismiss a heavyweight object once there are no clients that use it.

# Pros and Cons
|Pros| Cons |
|--|--|
| You can control the service object without clients knowing about it. | The code may become more complicated since you need to introduce a lot of new classes. |
| You can manage the lifecycle of the service object when clients don’t care about it. | The response from the service might get delayed. |
| The proxy works even if the service object isn’t ready or is not available. |  |
| _Open/Closed Principle_. You can introduce new proxies without changing the service or clients. |  |


