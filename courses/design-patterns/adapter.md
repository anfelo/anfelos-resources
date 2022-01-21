# Intent

> **Adapter** is a structural design pattern that allows different objects with different interfaces to communicate and collaborate.

# Problem

Imagine you are building a platform that gathers weather data from different sources. You find that the most relevant web APIs return the information in XML format, so you decide to build an XML parser to convert the data to something you can work on your platform.
Some years have passed and you decide to add a weather prediction feature to your app. In addition, there is a very popular library that does all the heavy lifting for you. The only problem is that this library works only with JSON formatted data.

Now you face a dilemma, you could change the library to work with XML data, but this is only possible is you have access to the source code. You could also look for another library that works with XML directly, but you might not be so lucky.

# Solution

The solution is to create an _adapter_ object. This _adapter_ is in charge to mutating the interface of one object so that another object can communicate or use its methods.

The way the _adapter_ works is by wrapping one of the objects to hide the complexity of the conversion happening. This way other objects can communicate with the wrapped object with the same interface without knowing that a translation/conversion is happening. Here is how it works:

1.  The adapter gets an interface, compatible with one of the existing objects.
2.  Using this interface, the existing object can safely call the adapter’s methods.
3.  Upon receiving a call, the adapter passes the request to the second object, but in a format and order that the second object expects.

In the case presented before, you could create an XML to JSON adapter for each of the parts of the library you are planning to use. That way, when a new weather prediction need to be made, the app passes the XML to the adapter, the adapter then converts the XML into a JSON format that the library expects.

# Structure

There are two main types of adapter implementations, the _object adapter_ and the _class adapter_. Let's take a look at the _object adapter_ first:

## Object Adapter

The adapter implements the interface of one object and wraps the other one.

1.  The **Client** is a class that contains the existing business logic of the program.
2.  The **Client Interface** describes a protocol that other classes must follow to be able to collaborate with the client code.
3.  The **Service** is some useful class (usually 3rd-party or legacy). The client can’t use this class directly because it has an incompatible interface.
4.  The **Adapter** is a class that’s able to work with both the client and the service: it implements the client interface, while wrapping the service object. The adapter receives calls from the client via the adapter interface and translates them into calls to the wrapped service object in a format it can understand.
5.  The client code doesn’t get coupled to the concrete adapter class as long as it works with the adapter via the client interface. Thanks to this, you can introduce new types of adapters into the program without breaking the existing client code. This can be useful when the interface of the service class gets changed or replaced: you can just create a new adapter class without changing the client code.

## Class Adapter

This implementation uses multiple inheritance to allow the adapter get the methods from both, the service and the client, without wrapping one or the other.

1. The **Class Adapter** inherits the behavior from both, the client and the service and the implementation details happen within the overridden methods.

# Pseudocode

Let's take as an example
This is a test of update
