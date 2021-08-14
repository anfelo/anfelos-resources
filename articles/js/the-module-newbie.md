Wouldn't it be nice to pack related code in a single container with a well structured and clear interface. This code would not only prevent global scope pollution but it also would be easy to understand by just looking at its interface. 

The way to achieve this in JavaScript is through **modules**. Modules in JS are used to create well organized code responsible for doing a well defined task. 

Modules should first specify which pieces of code they depend on (dependencies) and which functionality they provide to other modules (Interface). 

**Dependencies:** Other pieces of code (modules) that are required for the module to work. This must be especified at the begining of the module.

**Interface:** Pieces of functionality a module offers to other modules or JS modules. These can be objects, variables and classes and functions. Any piece of functionality not specified in the interface, remains hidden to the outside world. 

For the purpose of explaining how modules are created and how they work I'm going to use as an example a coffee machine. For simplicity, this machine should be able to make only capuchinos, and espressos.

### My First Module

The most simple type of module that can be constructed in JS is one where a local scope is created using a self-calling function or IIFE (Immediately Invoked Function Expressions). In addition, this module contains some private functionality, only accesible inside the module ifself, and this module also returns an object with the functionality available to other modules (Interface). See code snippet below:

```javascript
const coffeMachine = function() {
  
  // A private object
  const preparations = {
    'capuchino': 'Here you have your capuchino with cream and cinnamon powder!',
    'espresso': 'This espresso will most certainly wake you up! Enjoy it!'
  };
  
  // The interface
  return {
    makeCoffee(type) { return preparations[type]; },    
  };
  
}();
```

Thats it! my first module, now i'm ready to use it every time I want some coffee. 

But wait, there's something missing. Yup, we didn't specify any dependencies. The module is so simple that it doesn't require any dependency, but what if our implementation of the module grows to perform more complicated stuff? 

In the next section I introduce you to CommonJS Modules that are capable of dealing with dependencies.


### CommonJS

CommonJS is the most widely used approach to create modules in JavaScript. This type of modules are constructed around a function called `require()` which is in charge of loading other modules by their name and return its interface. Additionally, this type of modules use an object called `exports` as the returned interfaceof the module. All public functionality should be added to the exports object as we will see later.

For the purpose of explaining this concept, I'm going to expand the implementation of my `coffeeMachine` module, which will require another module (`levelize`) that receives as a parameter a number from 1 to 10 and returns a string representing the level of bitterness of the coffee. See code snippet below:

```javascript
// Dependency declaration
const levelize = require('levelize');

// A private object
const preparations = {
  'capuchino': 'Here you have your {lvl} capuchino with cream and cinnamon powder!',
  'espresso': 'This {lvl} espresso will most certainly wake you up! Enjoy it!'
};

// Interface of the module added to the exports object
exports.makeCoffee = function(type, num) {
  // Use of the dependency module
  const bitterness = levelize(num);
  
  // Return the results
  return preparations[type].replace('{lvl}', bitterness);
}
```
As you can see all dependencies are declared and loaded at the begining of our module, and then used when the `makeCoffee` function is called. Another aspest to point, from the code snippet, is the use of the `exports` object, available in the local scope of the module, to create the interface of our module. 

Now, the way we would use this module, assuming we are requiring it from another program which is located in the same directory of the module, can be seen in the next code snippet:

```javascript
const { makeCoffee } = require('./coffeeMachine');

console.log(makeCoffee('espresso', 10));
// -> This very bitter espresso will most certainly wake you up! Enjoy it!
```
See how we imported our module? Isn't that nice and easy? 

However, the `require` function is not implemented in the JavaScript languange but it is built on Nodejs which is the JavaScript runtime. Node is capable of reading and loading dependency modules for our module.

See the minimal implementation of the `require` function in [Eloquent JavaScript](http://eloquentjavascript.net/10_modules.html).

### AMD

AMD or Asynchronous Module Definition, is a form to declare and load dependencies for our modules. It is meant to solve some issues with CommonJS by asynchronously loading the dependencies and the module itself. For more information check [Learning JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/) and [Dojo's Introduction to AMD](https://dojotoolkit.org/documentation/tutorials/1.10/modules/index.html).

The main concepts around AMD are:

1. **`define` method:** Facilitates module definition.
2. **`require` method:** Handles dependency loading.

#### Define

This method is used to define named or unnamed modules. This method takes 3 parameters, a module id, an array of dependency names and a function for instantiating the module (factory function).
See code snippet below:

```javascript
define(
  'module_id',  // Optional 
  [dependencies], // Optional
  definition function
);
```
This code snippet shows represents the signature of the module. The `module_id` is used when concatenating various modules. Additionally, if the `module_id` is abscent we get an anonymous module.

Now we can implement our `coffeeMachine` with AMD as follows:

```javascript
define(
  'coffeeMachine',
  ['levelize'],
  function (levelize) {
    // A private object
	const preparations = {
	  'capuchino': 'Here you have your {lvl} capuchino with cream and cinnamon powder!',
	  'espresso': 'This {lvl} espresso will most certainly wake you up! Enjoy it!'
	};
	
    // Use of the dependency module
    const bitterness = levelize(num);
    
    // Return the module's interface
    return {
      makeCoffee: function() {
        return preparations[type].replace('{lvl}', bitterness);
      }
    };
});
```

As you can see, we used the `define` function to specify and load our dependencies, this dependencies are then mapped as parameters for the module implementation function. Finally, the module's interface is returned.


#### Require

Just like CommonJS modules, AMD uses the concept of dependency loading with a function called `require`. The main difference is that the dependency loading is done asynchronously. This function takes two parameters, an array with the modules' names and a callback that is executed as soon as the dependencies are loaded. See code snippet below:

```javascript
require(['levelize'], function(levelize) {
  console.log('levelize was successfully loaded!');
});
```

Both `define` and `require` can be used to load dependencies dynamically as follows:

```javascript
define(function(require) {
  let isReady = false,
    makeCoffee,
    preparations = {
	  'capuchino': 'Here you have your {lvl} capuchino with cream and cinnamon powder!',
	  'espresso': 'This {lvl} espresso will most certainly wake you up! Enjoy it!'
	};

  require(['levelize'], function(levelize) {
    isReady = true;
    
    // Use of the dependency module
    const bitterness = levelize(num);
    
    makeCoffee = function() {
      return preparations[type].replace('{lvl}', bitterness);
    }
  });

  return {
    isReady: isReady,
    makeCoffee: makeCoffee
  };
});
```
Nice! we did it! We created a AMD module for our `coffeeMachine`. 

The next type of modules are the future for defining and importing modules in JavaScript. ECMASCRIPT Modules.

### ES Modules
 
CommonJS and AMD have been the standards for writing modules for long time, and developers have adopted them because there was not an integrated way to do it in the languange. 

ES  or **ECMASCRIPT** modules come with an integrated way to import and export modules in JavaScript. This way is feels more natural and is also more readable. 

#### `import` :

ES modules use two new keywords to import modules, `import` and `from`:
```javascript
import levelize from 'levelize';

const bitterness = levelize(num);
```

#### `export`:

This new keyword lets us specify the interface of our module. `export` can be followed by a function, class, varible and object to expose the desired functionality:
```javascript
...
export function makeCoffee() {
  return preparations[type].replace('{lvl}', bitterness);
};
```

#### `default`:

This keyword goes after the `export` keyword to specify the module's main exported value:

```javascript
...
export default function makeCoffee() {
  return preparations[type].replace('{lvl}', bitterness);
};
```

This looks cleaner than CommonJS or AMD don't you think?

The future of JavaScript modules clearly goes in this direction. However, the community is still adopting the technology and browsers support has some issues. Many of the ES modules are automatically converted into other formats for better support.

### Conclusion

In this short article, I wanted to understand what are modules and why they are needed when programing with JavaScript. Aditionally I introduced the most common types of module formats. The following topics where covered:

1. What are modules in JavaScript and why are they needed.
2. CommonJS modules: The most widely format for writing modules. 
3. AMD Format: A way to improve the functionality of CommonJS asynchronously loading dependencies.
4. ES Modules: The future of JavaScript module formating.

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!