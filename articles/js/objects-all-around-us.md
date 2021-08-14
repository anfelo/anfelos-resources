Objects are one of the key concepts in JavaScript. They can be compared to classes in other programing languages and can be used to architecture our programs in a more organized way. We can divide our program in multiple objects responsible for doing one part of the functionality and describing the data model. This is a very organized way of writing software, makes it more concise and manageable. In this article, I go through the basic Objects in JavaScript, explaining how they are created and why they are useful to become better JavaScript programmer. 

### What are objects?

Objects are a major topic in many programming languages and JavaScript is not the exception. Although, as we will see JavaScript handles this concept in a different way. 

**There are 9 built-in objects in JS:**

* String
* Number
* Boolean
* Object
* Function
* Array
* Date
* RegExp
* Error

All of these built-ins share a common ancestor from which they inherit many of its basic functionalities. The Object built-in. See code snippet below:

```javascript
const chiliName = new String('habanero');

console.log(typeof chiliName); // string
console.log(chiliName instanceof String); // true
console.log(chiliName instanceof Object); // true
```

With `typeof` and `instanceof` we can inspect anything in JavaScript and determine the primary type and if it is an instance of an object or not. In this case, `chiliName` was created with the construct form which makes it a Object because String inherits from Object.

### What are objects made of?

Objects have attributes or properties that describe the object (e.g. name, color, age, etc.), this is a way objects can be used to store information. Objects have methods, these are actions an object can perform (e.g. salute, jump, blink, etc.) and these actions can reference the object’s properties.

In JavaScript there are two ways of creating objects: the **declarative** (literal) way and the **constructed form**. See the next code snippet:

```javascript
// Declarative (literal)
const chiliOne = {
  type: 'habanero',
  'spice level': 'very hight',
  color: 'orange'
}

// Construct form
const chiliTwo = new Object();
chiliTwo.type = 'chipotle';
chiliTwo['spice level']= 'high';
chiliTwo.color = 'dark red';
```

The declarative way is the most common way to create objects because attributes and methods are listed all in one place. As you can see in the code snippet above, when using the construct form, the object is first created without any property and then all of the attributes and methods can be added. Additionally, attribute names containing spaces should be declared using quotes.

### Accessing properties

We created some attributes and methods for our `chiliOne` in the last section, but how can we access these properties later in our program?

In order to access an object’s properties, there are two ways. Using the **dot** notation, also called the property access, and using the **square bracket** notation or key access. See code snippet below:

```javascript
// dot notation
console.log(chiliOne.type);  // habanero

// square bracket notation
console.log(chiliOne['spice level']); // very hight
```

As we can see, both notations offer the same functionality. Both gives us access to the property value. However, they are used in different situations. Plus, **square bracket** notation offers the opportunity to programmatically create the key name to access the property value. See code snippet below:

```javascript
const foo = 'spice';

console.log(chiliOne[foo + ' level']); // very high
```

### Method or Function?

In theory, methods and functions can be used interchangeably in JavaScript because methods are just properties that hold a reference to a function. However, these methods have something different from normal functions. They hold a reference to the object with the keyword `this` [(See my article on this)](http://www.anfelo.com/articles/javascript/1/). Methods can access the object’s properties with `this`. See code snippet below:

```javascript
const chiliOne = {
  type: 'habanero',
  changeColor: function() {
    this.type = 'jalapeño';
  }
};

console.log(chiliOne.type); // habanero

chiliOne.changeColor();

console.log(chiliOne.type); // jalapeño
```

As you can see from the code snippet, inside the method, `this` holds a reference to the created object and therefore it can modify or access its properties. 

### The Prototype

All objects have a **blueprint**. As I told you before, all objects in JavaScript have a common ancestor, the Object built-in, and they inherit what is called the [Object.prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype). This is a set of attributes and methods that all the objects have without having to specify them.  Some of the most useful Object.prototype methods are [Object.hasOwnProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty), [Object.keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) and [Object.getOwnPropertyNames](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames). See code snippet below:

```javascript
Object.defineProperty(chiliOne, 'size', {
  value: 'small',
  enumerable: false
});

console.log(chiliOne.hasOwnProperty('type')); // true

console.log(Object.keys(chiliOne)); // [ 'type', 'spice level', 'color', 'changeColor' ]

console.log(Object.getOwnPropertyNames(chiliOne)); // [ 'type', 'spice level', 'color', 'changeColor', 'size' ]
```

As you can see, the `hasOwnProperty` method asks if the object has a specified property but it won’t look in the prototype. The `keys` method, returns an array with all the enumerable properties of an object. And the `getOwnPropertyNames` method returns an array with all the properties including the non-enumerable ones.

### Looping through properties 

JavaScript offers a simple way to loop through an object’s properties, the `for in` loop. This lets us access all the enumerable properties of an object. See code snippet below:

```javascript
for (let prop in chiliOne) {
  console.log(prop, chilOne[prop]);
}
// type jalapeño
// spice level very hight
// color orange
// changeColor function () { this.type = 'jalapeño'; }
```
Only the enumarable properties will be accessible in the `for in` loop. Notice that using the **square bracket** notation we can access the value of certain property, this is imposible using the **dot** notation.


### Conclusion:

In this article, we learned the basics of Objects in JavaScript. This concept will be very useful when applying OOP to construct a program as well as to understand the ‘gears’ that move the JavaScript language. 

The following topics where covered in this article:

**1. Object creation:** We went through the two ways of creating JS objects, the declarative(literal) way and the constructed way.

**2. Attributes and methods:** Objects have attributes, which are a way to store data related, and methods are like actions that an object can perform. 

**3. The Object.prototype:** All objects in JS share a blueprint, the Object.prototype, which is a set of built-in attributes and methods all objects can access.

**4. Useful prototype methods:** We talked about `hasOwnProperty`, `keys`, `getOwnPropertyNames` but there are more. [(See the docs)]( https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype) 

This was a very basic article on JavaScript objects, I encourage you to dig deeper in this topic with [You don’t now JS](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md) and [Eloquent JavaScript](http://eloquentjavascript.net/). 

Also check [treehouse](https://teamtreehouse.com/home) which has really great courses and teachers in all web technologies. 

If you want to learn more about JavaScript stay tuned because I will keep generating content on the concepts I struggle the most when learning the language.

Check my article on [this](http://www.anfelo.com/articles/javascript/1/) which I struggled a lot when learning JavaScript. 

<hr>
You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence! 

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!
