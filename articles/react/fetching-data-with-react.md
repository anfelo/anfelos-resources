Something any web or mobile app needs at some point, is data fetching. In this short article we will cover two ways to fetch data in a react application. 

React does not come with a data fetching feature integrated like Angular. It requires third party libraries to do this job. The good news is that there a different options to go with for fetching data to an external server. 

### What to expect from this article

We will cover how to fetch data using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) and a third party library called [Axios](https://github.com/axios/axios). Both options use **Promises** to handle the response from the external source. 

### Where Should I Fetch In My App?

The best place to fetch data in a React App is the `componentDidMount` lifecycle method of  a class component. This is because by the time the method is called, there is already a dom representation of the component that will render the data.

### Fetch API

The Fetch API provides the necessary tools to make requests for resources. The api exposes a method called `fetch()` which returns a Promise that resolves to the data we are trying to fetch or an error from the request. The API is implemented in many modern browsers so there is no need to import any dependency. See an example below.

```javascript
...
componentDidMount() {
  fetch('someUrl')
    .then(response => response.json())
    .then(responseData => {
      this.setState({
        list: responseData.data,
        loading: false
      });
    })
    .catch(err => console.log('Error: ', err));
}
...
```

In the code snippet presented above, we call `fetch` passing the URL where we want to request resources. We can then obtain the resources with `then`, which is used to describe what to do when a Promise resolves. The first call to `then` will take the raw data and convert it to JSON format. In the second call to `then` we can update the state of our app. Finally with `catch` we handle errors that occur during the request.

Easy right? 

However, there is one thing to have in mind when working with the Fetch API. It is not widely supported by all modern browsers. Luckily there is a [polyfill](https://github.com/github/fetch) for this, so make sure to include it in your app.

### Axios Library

Axios is a third party JavaScript library, used for fetching data but also for making POST request. It works very similarly to the Fetch API. 

First install Axios:

```bash
> yarn add axios
```

Then import it and use it:

```javascript
import axios from 'axios';

...
componentDidMount() {
  axios.get('someUrl')
    .then(response => {
      this.setState({
        list: response.data,
        loading: false
      });
    })
    .catch(err => console.log('Error: ', err));
}
...
```

Axios exposes different methods. In the last example, we used the `get` method which takes a URL as a parameter and returns a Promise. With the `then` method we can handle the successful resolution of the Promise. Notice we didn't have to parse the data to JSON. Axios takes care of that. 

Axios is a powerful library. You can pass different configuration parameters, make post request to a server, and more. If you want more information, [see the documentation](https://github.com/axios/axios).

### Conclusion

This was a very short article on how to fetch data with React. However, the methods discussed can be used with any JavaScript library or framework.  the following was covered by this article:

* `componentDidMount` is the best option on where to place the fetching logic of the React app because a DOM representation of the component is already created.
* The Fetch API is integrated in many modern browsers, therefore, there is no need to install any package.
* Add a polyfill when using the Fetch API for better browser support.
* Axios is a third party library used for making GET and POST requests. It is widely supported by modern browsers.


---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence! 

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!

