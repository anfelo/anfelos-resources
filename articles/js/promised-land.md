Promises are one of those JavaScript topics that was really hard for me to grasp during my learning journey. This is why I decided to write this short article where I explain the concept and ilustrate how it is used with some examples. Who knows, maybe you'll find the Promised land after reading this article!

I will asume that you are already familiar with **callbacks** in JS and that you have experienced the [callback hell](http://callbackhell.com/), or also called *"pyramid of doom"*. I like more the later. 
If you want to learn more about **callbacks** and **asynchronous** JavaScript, I recommend [Eloquent JavaScript's](http://eloquentjavascript.net/11_async.html) chapter on asynchronous programming and [You don't know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20&%20performance/README.md#you-dont-know-js-async--performance). 

### What to expect from this article?

This article is a basic introduction to promises in JavaScript where you can find the following:

* What is a Promise?
* Why do we even need Promises in the first place?
* The three states of a Promise
* How to handle success
* How to handle failure
* Basic usage of Promises

### What is a Promise?

In [Eloquent JavaScript](http://eloquentjavascript.net/11_async.html) a Promise is described as *"an asynchronous action that may complete at some point and produce a value"*, or as *"a promised value that might already be there or might appear in the future"*. Another key aspect is that this *"promised value"* can indicate success or failure (this will be explained later). 

This concept was integrated into the JavaScript environment with the realease of ES6 and know is widely supported by modern browsers. 

#### Why do we even need Promises in the first place?

Promises are an abstraction of callbacks. they solve various pitfalls when working with callbacks to perform asynchronous actions. One of the problems solved is the so called *"callback hell"* (See below), which torments many programers (junior to more experinced ones).

```javascript
const getChiliTypes = () => {
  // Make a first ajax call (asume 'ajax' is a valid ajax call)
  ajax(someUrl, (firstResults) => {
    if (firstResults.length > 0) {
      firstResults.forEach((item) => {
        // Make another ajax call with the results
        ajax(someUrl + '?q=' + item, (secondResults) => {
          // Do something with the results of the second ajax request
        });
      });
    }
  });
}
```

Can you see how this can rapidly escalate into *"hell"*?

When you need to make asynchronous actions with the results of another asynchrounous operation you will most certainly face this problem. Sure, there are ways to avoid this issue. However, the most efficient way to solve this issue is with Promises as you will see later.

Other issues solved by Promises are:

* Lack of sequentiality
* Lack of trustability

I won't go into detail in this aspects. If you want to learn more about the pitfalls that Promises solve, I recommend reading [You don't know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch2.md) chapter 2 on callbacks. 

### The three states of  a Promise

Promises have three states, **pending**, **resolved** and **rejected**. They can only have one state at any time and once it is resolved or rejected, its value can't be modified with external code.

When a Promise is first created, and the value is still not there, the Promise is waiting on the value to be resolved or rejected. A promise can be created with its constructor passing an **executer** callback as the first parameter. This **executer** should contain a success handler (`resolve` callback) and a failure handler (`reject` callback). See code snippet below.

```javascript

var myPromise = new Promise((resolve, reject) => {

  // The promise is pending until either resolve or reject are called
  if(good_condition) {
  
    // When resolve is called the Promise changes its state to resolved
    resolve('success :-)');
  } else {
  
    // When reject is called the Promise changes its state to rejected
    reject('failed X-(');
  }
  
});
```

#### Handling success

After a Promise has been resolved, there is a way to access it's value. Promises have a method called `then()` that can be called to describe what to do with the resolved value of a promise. This method returns a promise as well, so another `then()` can be chained after it. See code snippet below.

```javascript
const getChiliTypes = new Promise(...);

getChiliTypes
  .then((items) => {
    // Executes when the 'getChiliTypes' Promise resolves
    return items.join(', ');
  })
  .then((str) => {
    // Executes when the previous 'then' resolves
    console.log(str);
  });
```

As you can see, the Promise is first created and awaits to be resolved. After that, the first `then()` method is executed with the resolved value of the promise and the the next `then()` is executed. 

Really simple don't you think? Its also cleaner that the *"callback hell"*.


#### Handling failure

But what happens if in the middle of any of the asynchrounous processes, performed by a Promise,  an error occurs or the reject callback is called? 

There a different ways to handle errors or failure. The first one is to pass as asecond argument to the `then()` method a callback to hable the error. See example below.

```javascript
getChiliTypes
  .then((value) => {
    ...
  }, (err) => {
    // Catches any errors occured at any point of the chain.
    console.log(err);
  });
```

The second alternative to handle errors, occured during the async process, is the Promises' `catch()` method that can be chained but has to go at the end of the chain.
This method will catch any errors occured during the Promise chain by default. See example below.

```javascript
getChiliTypes
  .then((value) => {
    ...
  })
  .catch((err) => {
    // Catches errors by default, that occured in any step of the chain
  });
```

### Useful static methods

There are some static methods that can be used to generate a Promise. Two of the most commonly used are the `Promise.all([..])` and `Promise.race([..])` methods.

The `Promise.all([..])` method can deal with two or more paralel processes. This can be two ajax requests running at the same time. The Promise has to wait for both processes to finish before it can be resolved. See example below.

```javascript
const p1 = request(urlOne);
const p2 = request(urlTwo);

Promise.all([p1, p2])
  .then((results) => {
    // 'results' is an array of values resolved from p1 and p2
  })
  .catch((err) => {
    // handle errors or rejections
  });
```

One thing to add here is that if one of the Promises is rejected, the other is automatically rejected too. 

`Promise.race([..])` works as the name implies, as a race between one or more Promises. The first one to resolve is used as the resulting value of the expression and can be accessed in the next `then()` method. The other Promises are rejected. [See the documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)


### Conclusion

Promises came to save the day for javascript programers. Now asynchronous tasks are easier to understand and more manageable. 
This is what was covered by this article:

* A Promise holds a value that might already be there or might appear in the future.
* Promises have 3 states, resolved, rejected and pending. Once it is resolved or rejected, it becomes an inmutable value. 
* `then()` can be chained to a Promise to some action with the resolved value. 
* Any errors or exceptions can be handled by chaining `catch()` athe end of the Promise chain.
* Use `Promise.all([..])` to deal with two or more async processes in parallel.
* Use `Promise.race([..])` to get the value out of the fastest Promise.


If you want to learn more about JavaScript stay tuned because I will keep generating content on the concepts I struggle the most when learning the language.

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!