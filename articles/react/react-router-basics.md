Web sites require a way to display different pages when a different URL is requested. Frameworks like **Angular** or **Vue** have integrated features for handling the routing of the web app. However, **React** is just a UI library and it does not have implemented a routing mechanism. It relies on third party libraries to handle this behavior. 

In this article we will learn the basics of **React Router v.4**, a third party powerful library build for handling routing in React apps. 

### What to expect from this article?

We will go through the basics of the library, explaining how all the features fit together and how they are implemented in React. After reading this article you will learn how to:

* Set a `BrowserRouter` component to keep the UI in sync with the URL
* Set different `Route` components to handle rendering of the App components
* Navigate between routes with `Link` and `NavLink` components
* Redirect to different `Route` components
* Display 404 Custom components with `Switch`
* Use `match` object to access path, parameters and url information
* Navigate to a Route programmatically with the `history` object

### Getting Started

First make sure you install `react-router-dom` which is the package that contains the React components.

```bash
> yarn add react-router-dom
```

Now you are set!

### Main Router Components

React Router has two main components, `BrowserRouter` and `Route`. The first one, is the root routing component and it helps to keep the UI in sync with the URL. `Route` renders an App's component when the URL matches the `Route` component. See an example below:

```javascript
import React from 'react';
import {
  BrowserRouter,
  Route
} from 'react-router-dom';

// App Components
import Home from './Home';
import About from './About';
import Contact from './Contact';

const App = () => (
  <BrowserRouter>
    <div className="container">
      <Route path="/" component={ Home } />
      <Route path="/about" component={ About } />
      <Route path="/contact" component={ Contact } />
    </div>
  </BrowserRouter>
);
```

By wrapping the root component with `BrowserRouter` we will have more control on what is rendered into the screen.

The `Route` components have two properties in the last example, `path` and `component`. With `path` we specify the path we want to match with our component. We pass the component to the `component` property so it can be rendered. 

There is a small bug in the last example, when we navigate to `"/about"` or `"/contact"` in the url,  the component `Home` will be rendered. this is because `"/"` is also matching with both paths. We should specify the `Route` that we want to match the **exact** path. See code snippet below:

```javascript
...
<Route exact path="/" component={ Home } />
...
```

With the `exact` property, we tell the Route component to match the exact path.

Another way to render a component with `Route`, is by specifying a render property that takes a callback. This callback should return a component to render. 

```javascript
...
<Route path="/about" render={ () => <About title="About" /> } />
...
```

Instead of using the `component` property, we specify a `render` property that renders a component. The difference is that we can pass properties to that component like a title.

### Navigating Between Routes

There a two ways to implement links with React Router. We can use a `Link` component that will render a simple `<a href=""></a>` tag. 

```javascript
import { Link } from 'react-router-dom';

const Navbar = () => (
  <ul>
    <li><Link to="/">Home</Link></li>
    <li><Link to="/about">About</Link></li>
    <li><Link to="/contact">Contact</Link></li>
  </ul>
);
```

With `Link` we just need to specify a `to` property which is the same as `href`.
The problem with `Link` is that we can't know if a navbar link is active or not. For this we have `NavLink` which gives and **active** class when when we navigate to that link.

```javascript
import { NavLink } from 'react-router-dom';

...
<NavLink to="/">Home</NavLink>
// this renders: <a href="/" class="active">Home</a>
...
```

If you want a custom **active** class, set the `activeClassName` property or set the active styles with the `activeStyle` property. 

```javascript
...
<NavLink to="/" activeClassName="nav-link--active">Home</NavLink>
// this renders: <a href="/" class="nav-link--active">Home</a>

<NavLink to="/about" activeStyle={{ color: "tomato" }}>About</NavLink>
// this renders: <a href="/about" style="color:'tomato'">About</a>
...
```

Now you can use some CSS to customize the look of your active nav links.

### Redirecting to Other Components

The `Redirect` component redirects the url to another `Route`. Just specify the `to` property. Most of the time, this component is rendered by another `Route` component, to redirect the user when a certain url matches the `Route` path.

```javascript
import { Redirect } from 'react-router-dom';

...
<Route 
  exact 
  path="/articles" 
  render={ () => <Redirect to="/articles/all" } />
...
```

When a user visits `"/articles"`, he/she will be redirected to `"/articles/all"`.

### Using 404 Custom Components

When a user visits a route that is not specified by the app, nothing will be rendered. This is why we need to implement a way to handle 404 Not found pages. With `Switch` we can wrap all the `Route` components to render only the first to match, and at the end declare a default `Route` component that does not have a `path` property.

```javascript
import { Switch } from 'react-router-dom';

...
<Switch>
  <Route exact path="/" component={ Home } />
  <Route path="/about" component={ About } />
  <Route path="/contact" component={ Contact } />
  <Route component={ NotFound } />
</Switch>
...
``` 

If the url does not match any of the `Route` components, the `NotFound` component will be rendered.

### The `match` Object

`BrowserRouter` sends it's `Route` children an object with information about the url, the path and the parameters that where passed through the url. This object is called `match` and it can be accessed by any component rendered by a `Route`.

```javascript
import React from 'react';
import { NavLink } from 'react-router-dom';

const Header = ({ match }) => (
  <NavLink to={`${match.path}/some-article`}>Some Article</NavLink>
);
```

Another example would be to send URL parameters from a `Route` component, and access the parameters in the rendered component.

```javascript
import React from 'react';
import { Route } from 'react-router-dom';

const Header = () => (
  <Route path={`articles/:title`} component={ Article } />
);

const Article = ({ match }) => (
  <h1>{ match.params.name }</h1>
);
```

Notice that to specify a URL parameter we need to add `:my-param` before the parameter name, which can be anything.


### Navigating Between Routes Programmatically

Similar to the `match` object, `Route` components have access to the `history` property, which contains information about all the URLs visited. Additionally, a new URL can be set by pushing it into the `history` array. 

```javascript
handleClick = e => {
  let newUrl = 'articles/react-router-basics';
  props.history.push(newUrl);
}
```

By adding `newUrl` to the `history` array, the user will be redirected the newly inserted URL.

### Conclusion

In this article we went over the basics of **React Router v.4**, a routing library build for React apps. We covered the following:

* `BrowserRouter` and `Route` are the main components exposed by the `react-router-dom` library
* With `BrowserRouter` we can maintain the URL and the UI in sync
* With `Route` we manage which parts of the UI should render when a certain path is requested
* We can navigate through the App routes with `Link` and `NavLink`
* `BrowserRouter` child components have access to information about the url, path and parameters with the `match` object.
* A `Route` can be programmatically assigned by pushing a new url to the `history` array.

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!