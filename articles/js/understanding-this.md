One of the things I’ve always struggled with JavaScript , until now, is the use and understanding of the keyword `this`. I’m always facing situations where I wish the concept was clear in my head to make my code cleaner and more [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).
 
As a JavaScript developer `this` is something you most certainly will come across. This is why I decided to dig deeper and understand the concept by writing this article. 

I consulted two of my favorite JS reference books, [You don’t now JS](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md) and [Eloquent JavaScript](http://eloquentjavascript.net/) and my favorite courses online in [treehouse](https://teamtreehouse.com/home), for wisdom in the topic and this is what I learned:

### The misconceptions:

Believing that `this` means 'itself' literally. It is common to think of `this` as the actual function that is using the reference. Which is incorrect and one should not interpret `this` keyword as literal as it sounds. See the code snippet below for an example:

```javascript
'strict mode';

function getChili() {
    this.color = 'orange';
}
getChili.color = 'red';

getChili();
console.log(getChili.color); // red
```

In the code snippet presented above, `this` is used to change the default color property of `getChili` function whenever it is called for the first time. However, this won’t work because `this` does not hold a reference to the function itself. At the moment `this` is executed, it holds a reference to either the global object or the value `undefined` due to how the function is called. I will explain the reason for this later.

The second misconception is to think that this creates a reference to the [lexical scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch2.md) of a function. The keyword `this` will never correspond to the functions’ scope, to access the function’s variables in some other place. See the next example where I illustrate this common error:

```javascript
'strict mode';

function getChili() {
	var color = 'orange';
	this.changeColor();
}

function changeColor() {
	this.color = 'green';
	console.log(this.color);
}

getChili(); // undefined
```

In these lines of code, which I have probably written in the past, I try to access a function’s variable inside another function thinking that `this` is a reference to my first function’s scope. This fails badly. The keyword `this` will never create that kind of bridge between both functions. 

Now that I have explained the **don’ts**, I want to take the time to try and explain when it is useful and how to use it as a powerful mechanism.

### `this` Unveiled:

How `this` is interpreted depends on how the function is invoked rather than how it is declared. Is it called from an owning object? Is it called enforcing an object reference with some useful function methods like [call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call), [apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) or [bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)? Am I creating a function with the keyword `new` or is it just a normal function call? 

The following are 4 situations where `this` takes a value:

**1. In a normal function call:**

`this` refers to the call-site (where the function was called). In the majority of the cases, it will refer to the global scope object unless we are running the script in `‘strict mode’`, where it will be `undefined`.

**2. Within methods on objects:**

When calling a function from an object sets the value of `this` to the object itself. It is used to access the objects’ properties and methods. See the code snipped below:

```javascript
'strict mode';

function setColor() {
   return this.color = 'red';
}

let chili = {
    color: 'green',
    spiciness: 'hot',
    changeColor : setColor
};

chili.changeColor();
console.log(chili.color); // red
```

This way of invoking a function is called **Implicit Binding**. The object `chili` owns some properties such as color and spiciness, and a method `changeColor` that holds a reference to a function. As you can see this function can successfully access the objects properties using `this`. In this case, `this` is referring to the owning object chili.  

**3. When a function is created with the keyword `new`:**

Using the keyword `new`  in JavaScript creates an object with `this` bound to the object itself. Notice that `this` does not correspond to the Constructor function (a.k.a object prototype), it corresponds to the object that is created or instantiated. This is true with functions as well, because functions are also objects in JS. See code snippet below:

```javascript
'strict mode';

function chili(color) {
    this.color = color;
}

const redChili = new chili('red');
console.log(redChili.color); // red
```

By simply placing `new` before the function name, a function is created with the value of `this` set to the function itself. In the code snippet we create a new function and asign it to the variable `redChili`. By using the keyword `new`, `this` holds a reference to the function itself and finally we can see that the color property of our chili was properly set to 'red'.

**4. A function invoked with call, apply or bind**

We can enforce a function to have a specified value for `this`. This is called **Explicit Binding** and can be achieved by using the handy built-in function methods **call** and **apply**. Both methods take as their first parameter an object to use as `this` keyword. 

```javascript
'strict mode';

function greenChili() {
	this.color = 'green';
}

const chili = {
	color: 'red';
}

greenChili.call(chili);
console.log(chili.color); // green
```

See how using these methods are helpful?  

The method **apply** works in a similar way as **call**. They differ only in the other parameters they accept.

Even with these methods, we can [lose the binding](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md) easily. For this reason there is an alternative method called **bind**, which hard binds an object to a function call. This will ensure that no matter how we call the function, if we use the **bind** method it will never lose the reference to this. See code snippet below:

```javascript
'strict mode';

function greenHotChili(spicyness) {
	this.color = 'green';
	this.spicyness = spicyness;
}

const chili = {
    color: 'red'
};

const greenChili = greenHotChili.bind(chili);

const spicyChili = greenChili('hot');
console.log(chili.color, chili.spicyness); // green hot
```

Wow! Now where talking!

Now we can safely affirm that `this` holds a reference to the chili within the function. 

**5. Extra: using ES6 arrow functions**

There is a new way of declaring functions with ES6 called arrow functions, that I won’t get into detail in this article (I’m working on an article to cover the ES6 newest features). However, I just wanted to mention that arrow functions bind `this` to the object or function that encloses them and it can’t be overridden even with the keyword `new`. I will big deeper with arrow functions and how this works inside of them in a future article so stay tuned! 

### Conclusion:

Steps to determine `this`:

1. Is the function called with `new`?  If so, `this` is the newly created object.
2. Is the function called with **call** or **apply** methods or with the **bind** method? Then `this` refers to the specified parameter object.
3. Is the function called with a context, meaning it is called upon a containing object? If so, `this` is the context object.
4. If none of the above happen, then `this` points at the default reference which is the global object or `undefined` when using `‘strict mode’`.

<hr>
You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence! 

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!
