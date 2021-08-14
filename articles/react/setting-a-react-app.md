When I was starting to learn how to create web apps with React, I learned about two different ways to set up a React development enviroment. One requires a some configuration, but is more customizable. The other is a 'one click' configuration. 

In this article, I go through both ways and present the pros and cons of both.

### What to expect from this article?

The topics that will be covered in this article are:

* Setting up a React Environment with Webpack
* Setting up a React Enviroment with `create-react-app`

Although we will do a basic configuration, nothing fancy, we will be using the following prerequisites and some knowledge in these topics is recommended.

### Prerequisites

* Webpack
* Babel
* ES6
* Yarn
* React

I will briefly introduce each concept but I recommend you to dig deeper on each of them.

#### Webpack:
Webpack is known as a module bundler. Amoung other things, webpack is incharge of our web app code and dependencies and bundle it into one single js file. For more information read the [documentation](https://webpack.js.org/).

#### Babel:
If we want to write code with the latest features of the JavaScript language, we will need a way to support this features in all browsers. This is where **Babel** comes in. 
Babel is a JS compiler, it compiles ES6 into ES5 and also, it compiles all the React code (writen in JSX) to ES5 code. [See the documentation](https://babeljs.io/)

#### Yarn:
Yarn is a package manager, just like NPM, but faster. We will use it to import all our dependencies needed for the App. [See the documentation](https://yarnpkg.com/en/)

#### React:
React is a JavaScript frontend library used to create reusable UI components. It was created by Facebook developers and its popularity has grown rapidly over the past years. [You can learn more from their website](https://reactjs.org/).

### Setting up a React Environment with Webpack 2

Webpack has now a new version, Webpack 4 which we will cover later. However, version 4 is still on beta so I will first show you how to do the setup with Webpack 2 and then we will see some upgrades brought by the version 4. 

#### Getting Started

First create a new directory for the react app, name it `hello-world` and initialize the app with yarn.

```bash
> mkdir hello-world
> cd hello-world
> yarn init
```

#### Webpack Installation and Configuration

The next step is to install all the webpack's dependencies.

```bash
> yarn add webpack webpack-dev-server path
```

You can see how this dependencies were added to the package.json file. 
Now we can configure webpack by creating a webpack.config.js file and adding the following code:

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve("dist"),
    filename: "index_bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: "style-loader" },
          { loader: "css-loader" }
        ]
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: "babel-loader"
      }, 
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: "babel-loader"
      }
    ]
  }
}
```

The webpack configuration has 3 main parts, the **entry** property, the **output** property and the **module** property. 

The **entry** is the entry point of our app, where we will be writting the react app. 

The **output** property tells webpack where we want to place the bundled file and what name to use. 

The last property is the **module** which is used to set all the transformations that our code will go through. For this example we have some **rules**, one for loading styles appropiatly and other for passing all our app files through the babel compiler.

#### Setting Up Babel

We now need to install some babel dependencies.

```bash
> yarn add babel-core babel-loader babel-preset-env babel-preset-react --dev
```

Next we need to add a babel configuration file, where all the babel presets will be added. Create a file named `.babelrc` and  add the following code:

```javascript
{
  "presets": [
    "env", "react"
  ]
}
```

#### Html Webpack Pluggin

There is one more thing to add to our webpack.config.js file, a very handy pluggin to create an html file with our bundle js file inserted on it. 
Let's first install the pluggin:

```bash
> yarn add html-webpack-plugin
```

Now in the webpack.config.js file add the following code:

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-pluggin");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve("dist"),
    filename: "index_bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: "style-loader" },
          { loader: "css-loader" }
        ]
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: "babel-loader"
      }, 
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: "babel-loader"
      }
    ]
  },
  pluggins: [
    new HtmlWebpackPlugin({
      template: "./src/index.html",
      filename: "index.html",
      inject: "body"
    })
  ]
}
```

Here we imported html-webpack-pluggin and added to the pluggins property. This pluggins takes a html entry file and creates a new one, in the dist folder, with our bundle js file inserted. 

#### React Components Setup

Now we can start writting our react app. 

First, create an src folder and create a file called index.html inside of it. Add the following code:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta  name="viewport"  content="width=device-width, initial-scale=1.0">
    <meta  http-equiv="X-UA-Compatible"  content="ie=edge">
    <title>React App Setup</title>
  </head>
  <body>
    <div id="root">

    </div>
  </body>
</html>
```

This html file will be the entry point of our app. 

Now create a file named index.js and add the following code:

```javascript
import React from "react";
import ReactDOM from "react-dom";

const App =  ()  =>  {
  return  (
    <div>
      <p>Hello React!</p>
    </div>
  );
};

export  default App;

ReactDOM.render(<App />, document.getElementById("app"));
```


#### Running the App

There is but one more thing to configure, that is the npm scripts for running and building our app. These should be configured in the package.json.
Open the package.json file in the root directory of our app, and add the following code:

```javascript
{
  "name": "hello-world-react",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  // Copy and paste these scripts
  "scripts": {
    "start": "webpack-dev-server"
    "build": "webpack"
  },

  "dependencies": {
  ...
  },
  "devDependencies": {
  ...
  }
}
```

Now we can build our app with the following command:

```bash
> yarn build
```

And run the development server:

```bash
> yarn start
```

That's it! Now just go and enjoy building a great app in the index.js.

#### Upgrades With Webpack 4

The main upgrades with Webpack 4 arrival are:

* Bundling performance improved
* Zero configuration and lots of great defaults

The first thing to notice is that with Webpack 4 we don't have to specify an entry point or an output path. By default, Webpack will look for an entry point in `./src/index.js`.
Similarly, when the build command is run, Webpack will create an output file in `./dist/main.js`.

Another greate improvement is the addition of a development and production mode in the package.json file. 

```javascript
...
"scripts": {
  "dev": "webpack-dev-server --mode development",
  "build": "webpack --mode production"
}
```

When building the project, `build` will take care of minification, scope hoisting and tree-shaking, just by adding the `--mode production`. Cool stuff! 

There are lots of great improvements that I won't describe in this article by I encourage you to look the [documentation](https://webpack.js.org/concepts/). There is also a great article on [Webpack 4](https://www.valentinog.com/blog/webpack-4-tutorial/) by Valentino Gagliardi.

### Setting up a React with `create-react-app`

Is good to know how every piece of the configuration works right? However, this process can get tedious when you start writting a bunch of react apps. 

If only there was a command that configured all the basic setup of my app. 

Oh! But there is one! 

Created by the guys at facebook, `create-react-app` is a powerful tool that lets you set all the initialization files with one command. Additionally, `create-react-app` provides some useful scripts to help you run a development server or build your app.

More cool stuff? `create-react-app`...

* Creates the necesesary files to make your app a Progressive Web App (PWA)
* Ships with Jest, a JavaScript unit tests library that lets you test react components.

#### Getting Started

Install `create-react-app`:

```bash
> npm install -g create-react-app
```

Now `create-react-app` is installed globally in your computer. The next step is to create the app. Run the command plus the name of your app in the directory that you desire:

```bash
> create-react-app hello-react
```

That's it! All the starter files have been created for you in the *hello-react* directory. Now run the app with the following command:

```bash
> npm start
```

This command automatically starts the app in the browser and listens to changes in the files. You can see your app live at `localhost:3000`.

#### Editing the App

To edit the app, just navigate to `./src/App.js` and start coding. Save your changes and you will see the dev server starting again automatically. 



#### Exposing Configuration Files

If you want to expose the configuration files (webpack, babel, etc.), `create-react-app` has a special command:

```bash
> npm run eject
```

You will see a directory called `config` where you can find the webpack.config.js and much more. 

**Note:** This process is irreversible, so use it with caution.

That's it! pretty easy right? 
I hope you enjoyed this article. I encourage you to go deeper into all the topics that were discussed here. 

### Conclusion

In this article we setup a development environment for React apps, with two different approaches. One lets you configure every aspect of the development environment and the other is a super useful one-command-tool to set up the starter files.

At the beginning of my react learning journey I was obsessed with each step of the configuration. Then I found out about `create-react-app` and I fall in love with it. Now I just want to start coding my apps as soon as possible.

Choose whatever fits you best and makes you happy. 

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!
