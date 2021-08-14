Animations bring life to html elements and a better user experience. In a normal web application, CSS and JavaScript are used together to create user interactivity. However, React applications manage what components are displayed, when they are displayed and how they should look. Whit React, animations are not handled the same way as a simple JavaScript application. 

> React is just a UI Library. It does not integrate animations on its own. 

React requires third party libraries for creating animations. Luckyly there is one nice Add-on called [ReactCSSTransitionGroup](https://reactjs.org/docs/animation.html) created and mantained by the community. 

### What to expect from this article

This is a short article that explains how to use the **ReactCSSTransitionGroup** Add-on. The topics presented are the following:

* Getting started
* How to set a CSSTransitionGroup
* What can be animated?
* Add the necessary styles to create the animation
* Adding custom CSS classes 


### Getting Started

First, install the add-on:

```bash
> yarn add react-transition-group@1.x

or

> npm install --save react-transition-group@1.x
```

Now we are set to start animating our components.

### How to set a CSSTransitionGroup

A `CSSTransitionGroup` is just a React component that contains `CSSTransitionGroupChild` elements. The children elements will be animated depending on some properties we set on `CSSTRansitionGroup`. See the code snippet below:

```javascript
import React from 'react'
import { CSSTransitionGroup } from 'react-transition-group';

const App = () => (
  <div>
    <CSSTransitionGroup>
      <li>One</li>
      <li>Two</li>
      <li>Three</li>
    </CSSTransitionGroup>
  </div>
);

export default App;
```

We have to wrap the elements that we want to animate with the `CSSTransitionGroup` component. All the `li` children will have now a `CSSTransitionGroupChild` associated, that will handle the animations. But this alone wont do anything.

### What can be animated?

The events that can be animated are:

* Child elements **entering** the dom
* Child elements **leaving** the dom
* The CSSTransitionGroup when mounted (appear)

```javascript
...
const App = () => (
  <div>
    <CSSTransitionGroup
      component="ul"
      transitionName="slide"
      transitionEnterTimeout={ 500 }
      transitionLeaveTimeout={ 500 }>
	  ...
    </CSSTransitionGroup>
  </div>
);
...
```

In the last code example, we set some porperties on the `CSSTransitionGroup` component. The `component` property, tell the add-on what element should be rendered in the DOM. The `transitionName` gives a name to the transition and will be added to the class names. The `trasitionEnterTimeout` tells the add-on the duration, in miliseconds, of the 'enter' animation of child components. The `transitionLeaveTimeout` tells the add-on the duration, in miliseconds, of the 'leave' animation of child components. 

There is a third type of animation. The 'appear' event will animate the first time the `CSSTransitionGroup` mounted. 

```javascript
...
const App = () => (
  <div>
    <CSSTransitionGroup
      component="ul"
      transitionName="slide"
      transitionEnterTimeout={ 500 }
      transitionLeaveTimeout={ 500 }
      transitionAppear={ true }
      transitionAppearTimeout={ 300 }>
	  ...
    </CSSTransitionGroup>
  </div>
);
...
```

Two more properties were added. The `transitionAppear` tells the add-on to register an animation for the entire group when first mounted. The `transitionAppearTimeout` specifies the duration, in miliseconds, of the 'appear' animation.

### Adding CSS Styles

By default, `CSSTransitionGroup` adds default CSS classes for each of the events. Two for each of the events `<transitionName>-<event>` and `<transitionName>-<event>-active`. The first one is used to describe the initial state of the animation and the last one specifies the final state of the animation. Both should be implemented in the CSS files of the app.

```css
...
/* 'enter' Event */
.slide-enter {
  transform: translateX(-100%);
}

.siled-enter.slide-enter-active {
  transform: translateX(0);
  transition: transform 0.5s ease-out;
}

/* 'leave' Event*/
.slide-leave {
  transform: translateX(0);
}

.siled-leave.slide-leave-active {
  transform: translateX(100%);
  transition: transform 0.5s ease-out;
}

/* 'appear' Event*/
.slide-appear {
  opacity: 0;
}

.siled-appear.slide-appear-active {
  opacity: 1;
  transition: opacity 0.3s ease-out;
}
```

Notice here that the duration specified in the transition should match the one specified in the component. Additionally, is recommended to concatenate the intial selector with the final selector for better specificity.

### Custom CSS Classes

The `transitionName` property accepts an object with the specified custom CSS classes for each event.

```javascript
const App = () => (
  <div>
    <CSSTransitionGroup
      component="ul"
      transitionName={{
        enter: 'enter',
        enterActive: 'enterActive',
        leave: 'leave',
        leaveActive: 'leaveActive',
        appear: 'appear',
        appearActive: 'appearActive'
      }}
      transitionEnterTimeout={ 500 }
      transitionLeaveTimeout={ 500 }
      transitionAppear={ true }
      transitionAppearTimeout={ 300 }>
	  ...
    </CSSTransitionGroup>
  </div>
);
...
```

Here we changed all the default `<transitionName>-<event>-active` to use camel-case naming like `.slide-enterActive`.

### Conclusion

That covers the basics of the `CSSTransitionGroup`. If you want to learn more about the add-on, you can read the [documentation](https://reactjs.org/docs/animation.html).

In this article we learned how to:

* Install the `CSSTransitionGroup` add-on.
* Set a transition group with `CSSTransitionGroup`.
* Set the properties of the component to animate its child components entrance, and exit of the DOM.
* Add the CSS styles that make the animation happen.
* Add custom CSS classes for each of the events.

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!