React is a JavaScript library for building UI components. It was created by the guys at Facebook, you can read more about it in their [official site](https://reactjs.org/). 
React apps can be written in plain JavaScript but it results easier to code in a more expressive language.

JSX is an extension of JavaScript designed to describe what the UI looks like and how it behaves. It looks like JavaScript and HTML at the same time but it is closer to JavaScript. 

### What to expect from this article?

This is an introduction to JSX for writing React web apps. We will cover the following topics:

* JSX anatomy
* Relation with JavaScript
* Embedding JavaScript expressions with JSX
* JSX is also an expression
* Attributes definition
* Spreading attributes
* Declaring children elements


### JSX anatomy

The first thing to consider is how JSX looks like. It looks like JavaScript and HTML at the same time. See example below:

```jsx
const elem = <p color="teal">This is an element!</p>;
```

As you can see, it looks like both JS and HTML but it is closer to JS under the hood. This code can be written in plain JavaScript and it would look like the following code:

```javascript
React.createElement(
  'p',
  { color: 'teal' },
  'This is an element!'
);
```
Both expressions do the same thing. However, the first one is easier to understand and more expressive. 


### Embed JavaScript with `{ }`

JavaScript can be embeded into JSX expressions with curly braces `{ }`:

```jsx
function sayHi() {
  return 'Hi there!';
}

const elem = <h1>{ sayHi() }</h1>;
// Same as: <h1>Hi there!</h1>
```
You can add any JavaScript expression between the `{ }` and JSX will understand it without a problem.

### JSX is also an expression

JSX can be used inside of an `if` statement or a `for` loop or as a return value of a function. JSX is also an expression on it own. 

```jsx
function sayHi( name ) {
  if (name) {
    return <p>Hi { name }!</p>';
  }
  return <p>Hi there!</p>;
}

const elem = <h1>{ sayHi('Andres') }</h1>;
```
Calling the `sayHi` function returns a JSX expression. This can be injected to the elem constant after it is evaluated.


### Attributes definition

Attributes can be added to the JSX expressions in two ways. As a literal string or as an expression, between `{ }`.

```jsx
const elem = <p className="text-large">Hi there, I'm a large text</p>;

const avatar = <img src={ someUrl } />;
```

As you can see, there are some attributes that don't look like HTML, the `className` instead of `class` attribute. This is due to the fact that JSX is closer to JavaScript than HTML.

### Spreading attributes

If an element has many attributes and you want to pass those with a single expression, the `...` expression can be used between `{ }` to successfully add the attributes. 

```jsx
function foo(props) {
  return <p {...props}>Wow!</p>;
}

const elem = foo({ className: 'text-bold', tabIndex: '0', ...});
```

In the last code snippet, when the `foo` function is called with and object containing all the attributes, the `elem` receives an element with all the attributes successfully assigned with `{...props}` expression.

### Declaring children elements

To specify children elements in a JSX expression, just nest them inside a container element.

```jsx
const elem = (
  <ul>
    <li>First Item</li>
    <li>Second Item</li>
  </ul>
);
```

Pretty simple and easy read!

### Conclusion

This was just an introductory article on JSX for React apps. If you want to learn more, see the tutorials in the [React documentation](https://reactjs.org/docs/introducing-jsx.html).

In this article we covered the following:

* JSX looks like JavaScript and HTML but it not any of those. It is a JavaScript extension.
* JSX is just a syntactic sugar for `React.createElemet()`
* Use `{ someExpression }` to embed JS expressions into JSX
* Attributes can be declared in two different ways, with string literals or dynamically with  expressions between `{..}`
* To declare children elements, just nest them inside the parent.

 ---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!