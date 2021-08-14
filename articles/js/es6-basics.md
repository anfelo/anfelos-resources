>Do you know the basics of JavaScript and want to keep moving forward in your learning journey?
>Do you want to learn about the latest sugars of the JavaScript language?
>
>Then this article will serve you you well.

**ES6** also known as **ES2015** or **ECMASCRIPT 2015** is the 6th version of the JavaScript language. The three terms reffer to the same thing and can be used interchangebly.
**ES6** describes a set of new features in the language. Some of these new features are just *syntactic sugar* but others are new functionalities as well. 


### What to expect from this article?

This article is a basic introduction to the new features, presented by **ES6**. The features I will go through are the following:

* `let` and `const` variable declaration
* Template literals or 'interpolated string literals'
* Arrow functions
* Default parameters
* Object literal extensions
* Spread/Rest operators
* Destructuring
* `for .. of` loops
* Classes


### Variable Declaration with `let` and `const`

For long time JavaScript developers have written their variable declarations with `var`. However, `var` can lead to unwanted behaviors due to how scoping works in JS. For example see this code snippet:

```javascript
function foo() {
  i = 5;
}

for (var i = 0; i < 10; i++) {
  foo();
  console.log('loop #' + i);
}
```

When the for loop executes, one would expect that the `console.log` should change its output on every iteration until `i` turns to 9. However, `var` (if not used within a function) will be scoped as a global variable and accessable in `foo`. This creates an infinite loop because every iteration the value of `i` is changed back to 5 in `foo` and never reaches 9. 

To solve this issue, `let` and `const` are *block scoped*, meaning they live only inside a block of code. A block of code can be a for loop, an if statement, or anything within two braquets `{ .. }`. Lets take a look at `let` first.

#### `let`:

The `let` keyword describes a variable that will change its value over the course of the program. The most common place to use this type of variable is in a for loop. See code snippet below:

```javascript
for (let i = 0; i < 10; i++) {
  let j = i * i;
}

console.log(i); // ReferenceError: i is not defined
```

As you can see, `i` can not be accessed from outside the for loop. In addition, the value of `i` and `j` changes with every iteration. 

#### `const`:

Similarly to `let`,  `const` is available only inside the block of code where it was declared. However, as you might expect, `const` is used to declare constant values, values that won't change after declared.  An exception will be thrown if one tryies to change its value. See code snippet below:

```javascript
const pi = 3.1416;

pi = 3.14159265359; // TypeError: Assignment to constant variable.
```

Both `let` and `const` are very useful and should be used instead of `var`. 

An important tip to know when to use one or the other is to start all variable declarations with `const` until the value of certain varible needs to change, then change it to `let`.


### Template Literals

Template strings, also called 'interpolated string literals', are a way to create strings that can expand accross multiple lines and can use placeholders for dynamic values. See below how we would got to create a string before ES6 template strings:

```javascript
const name = 'Andres',
  age = 25,
  profession = 'Web Developer';

// Single line string
const greeting = 'Hi my name is ' + name;

// Multiline string
const shortBio = 'Hi my name is ' + name + 
  '. I\'m ' + age + ' years old' +
  ' and I work as a ' + profession;
```

You should be falimiar with this way of creating strings. However, they can be hard to read and not very manageble (more in the case of the multiline string). 
Now compare the last code with the code snippet below where template strings are introduced:

```javascript
const name = 'Andres',
  age = 25,
  profession = 'Web Developer';

// Single line string
const greeting = `Hi my name is ${name}`;

// Multiline string
const shortBio = `
  Hi my name is ${name}.
  I'm ${age} years old
  and I work as a ${profession}.
`;
```

This is clearly more readable don't you think? 

So we just added our string inside two backticks ` `` `, additionally, we can create multiline strings without the need of `+` operator. More intrestingly, we can use `${..}` 
to whenever we want to add dynamic values. 

Are you starting to love **ES6** or what?


### Arrow Functions

One of the cool features of **ES6** is the new way to create functions. Arrow functions are created by using the arrow `=>` operator. See example below:

```javascript
const foo = (x, y) => {
  console.log(x, y);
};
```

There also some variations on how to create arrow functions:
```javascript
// No arguments, single line
const foo1 = () => 'Hi my name is Andres';

// 1 argument, single line
const foo2 = x => x * 2;

// Multiple arguments, multiline
const foo3 = (x, y) => {
  x = y + z;
  return x;
};
```

On the first two examples, if the code inside the function is only one line, the brakets can be omitted as well as the `return` keyword. For the second example, when the function only takes 1 argument, the `()` can be omitted.

Arrow functions also solve the well known `this` binding problem. They works as the `.bind(this)` works. 


### Default Paramenters

This feature is a great way to specify whether an argument of a function is optional or not, by giving it a default value. This is how we would create default parameters before **ES6**:

```javascript
// Or operator
function foo1(x, y) {
  x = x || 'Default value';
  ...
}

// Ternary operator (more acceptable)
function foo2(x, y) {
  x = (x !== undefined) ? x : 'Default value';
}
```

With both methods we should call the function passing both arguments. However, what if just want to pass a certain number of parameters, without breaking anything? See below hoe **ES6** default parameters work:

```javascript
function foo(x = 'Default value', y = 0) {
  console.log(x, y);
}

foo(); // 'Default value, 0'
```

Just assing the default parameters in the arguments of the function and thats it!

**Note:** Default parameters work more like the 'Ternary operator' method than the 'Or operator' method.


### Object Literal Extensions

Do you remember how object literals where created before **ES6** ? 

```javascript
const name = 'Andres',
  age = 25;
  
const person = {
  name: name,
  age: age,
  speak: function () {
    return `Hi my name is ${this.name}`;
  }
};
```

A little bit repetitive right? **ES6** comes with some extensions to wake code shorter. The first one is when the object property name is the same as the variable name I wnat to assign. We can just change `name: name,` with `name,`. See code snippet below:

```javascript
const name = 'Andres',
  age = 25;
  
const person = {
  name,
  age,
  speak: function () {
    return `Hi my name is ${this.name}`;
  }
};
```

The second feature is a way to make object's methods more concise by eliminating the `function()` part of the method. See how the `speak` method changes in the next code snippet:

```javascript
const name = 'Andres',
  age = 25;
  
const person = {
  name,
  age,
  speak() {
    return `Hi my name is ${this.name}`;
  }
};
```

Cool right? Now my object literals will look more concise and redable.


### Spread/Rest Operators

**ES6** introduced a new operator, the three dots `...`, which can have two different uses. The first is to *spread* out an array into its individual values. See an example below:

```javascript
const foo = ['b', 'c', 'd'];
const bar = ['a', ...foo, 'e'];

console.log(bar); // [1, 2, 3, 4, 5]
```

Another way to use this operator is the following:

```javascript
function foo(x, y, z) {
  console.log(x, y, z);
}

foo(...['a', 'b', 'c']); // 'a', 'b', 'c'
```

When using the `...` before an array, the contents will be split into its individual values.

The second use for the three dot operator is to gather a set of values into an array. This is also called the *rest* operator. It is often used to gather the rest of the parameters in afunction call. See code snippet below:

```javascript
function bar(x, y, ...z) {
  console.log(x, y, z);
}

bar('a', 'b', 'c', 'd', 'e'); // 'a', 'b', ['c', 'd', 'e']
```

The expression `...z` gathers the rest of the parameters, passed to `bar`, into an array. This feature is really useful when you don't really know how many parameters will be passed to a function when called.


### Destructuring

This feature, also known as *structured assignment*, helps to assign a number of variables with one single expression, without using a temporal variable. Lets first see how this worked before the arrival of **ES6**:

```javascript
// Working with arrays ##################################
function daysOfWeek() {
  return ['Mon', 'Tue', 'Fr'];
}

const tempDays = daysOfWeek(),
  monday = tempDays[0],
  tuesday = tempDays[1],
  friday = tempDays[2];

// Working with objects #################################
function personBuilder() {
  return {
    name: 'Andres',
    age: 25
  };
}

const tempPerson = personBuilder(),
  name = tempPerson.name,
  age = tempPerson.age;
```
 
From the examples presented above we can see how we need a temporal variable to hold the array or the objects value. Then we assign one by one the individual values to other variables. 

With **ES6** we can assign values to variables in a more concise way and without the need for a temporal variable. See code snippet below:

```javascript
// Working with arrays ##################################
function daysOfWeek() {
  return ['Mon', 'Tue', 'Fr'];
}

const [monday, tuesday, friday] = daysOfWeek();

// Working with objects #################################
function personBuilder() {
  return {
    name: 'Andres',
    age: 25
  };
}

const { name, age } = personBuilder();
```

In the code presented above, we created three variables `monday`, `tuesday` and `friday`. These variables were assigned the values returned from  `daysOfWeek` function. We also created `name` and `age` and assign them the values returned from `personBuilder`. 

As I see it, we *destruct* the object in the right side of the expression and assign each of its values to the variables in the left side of the expression.


### `for .. of` Loops

With `for .. of` loops we can loop over a set of values **(not the indexes)** produced by an iterator. See the example below:

```javascript
function daysOfWeek() {
  return ['Mon', 'Tue', 'Fr'];
}

for (let day of daysOfWeek()) {
  console.log(day); // 'Mon' 'Tue' 'Fr'
}
```

As you can see, the `for .. of` loop displays the values of the array returned from `daysOfWeek` rather than the indexes.


### Classes

JavaScript does not have classes like other object oriented programing languages. However, it tryies hard to look like one. **ES6** brings some syntactic sugar that makes classes look like real OOP classes.  The first thing is the introduction to the keyword `class` which is used to declare a class:

```javascript
class Person {
...
}
```

By convention, classes should be capitalized. Another new feature is a special `constructor` method which is called once the class is instantiated.

```javascript
class Person {
  constructor(name, age) {
    this.name = name || 'Felipe';
    this.age = age || 0;
  }
  ...
}

const person = new Person('Andres', 25);
```

As you can see, the `constructor` method can be used to set some of the properties of the class when instantiated.

**ES6** also provided some sugar for class inheritance with the `extends` keyword and the `super` method. Lets see these in action:

```javascript
class Person {
  constructor(name, age) {
    this.name = name || 'Felipe';
    this.age = age || 0;
  }
  ...
}

class Andres extends Person {
  constructor(name, age, profession) {
    super(name, age);
    this.profession = profession;
  }
  ...
}

const person = new Person('Andres', 25, 'Web Developer');
```

With `extends` we tell the program that the class `Andres` inherits from `Person` and with `super` we pass the parameters need by `Person` to be inicialized.


### Conclusion

**ES6** or **ES2015** brings some nice features into the table for  JavaScript developers. Some of them are just syntactic sugar while others extend the functionality of the language in a great manner. In this short article we went throught the basics of some of these features, this is what we discovered:

1. Declaring variables with `let` and `const` gives more power to variable scoping.
2. Template literals are more readable and manageble than normal strings.
3. Arrow functions are not just nicer more concise functions, they hard bind `this` without the need for `bind`
4. Know we can define default parameters just by assigning a value when describing the parameters of the function.
5. Object literals have shorter syntax for their properties.
6. The spread operator `...` can be used to expand an array or to gather items into an array.
7. Various variables can be assigned with a single expression by destructuring an object or array.
8. Want to loop over the values of an array and not its indexes? Use `for .. of` loops.
9. **ES6** brings some nice class implementation with `class`, `constructor()`, `extends`, `super()` and more.


We went through the basics of all these features. If you want to learn more, I recommend you to read [You don't know JS](https://github.com/getify/You-Dont-Know-JS/tree/master/es6%20%26%20beyond) on **ES6**. I also recommend [Treehouse](https://teamtreehouse.com/home), is a great learning platform full of great development courses, they have great teachers and relevant content. 

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!