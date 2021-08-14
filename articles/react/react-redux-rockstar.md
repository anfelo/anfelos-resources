A web app in React can grow fast to become a nightmare to maintain and manage. Think of having 100+ Components with the state being handled indifferent parts of the app. Not very nice. 

In this article we will explore Redux, a JavaScript Framework build to maintain and manage application state in a scalable, predictable and testable way. Redux can be implemented with any framework, but we will focus on React in this article. If you want to learn more about Redux you can visit their [documentation](https://redux.js.org/)

### What to expect from this article?

This article is an introduction to Redux and how it can be integrated with a React web app. We will discuss the following topics:

* Redux principles
* Installation of Redux
* Types of components and their role
* What are ActionTypes?
* What are Reducers?
* What are Actions?
* What is the Store?
* How to bind Action Creators?

### Key principles

Redux works under 3 main principles. Making use of these principles will allow an optimal integration with the framework.

1. Single source of truth:
The whole state of the application should be stored in a single place. **The store**

2. State is read-only:
The only way to change state is by generating actions (we will see this later in more detail) that describe what should happen to the state.

3. Changes to state are made with pure functions:
These pure functions receive 2 parameters, the previous state and an action, and return an object containing the new state. Their are also called reducers.

### Installation

In order to set up Redux to work with React we need to import 2 modules, `redux` and `react-redux`. The first one contains all the essential items to incorporate to a web app, and the second one is a library to help to connect with React.

```bash
> yarn add redux react-redux
```

Simple as that!

### Types of Components

In a React-Redux web app there are two types of components that should be divided in different directories within the `src` folder of your app, **Presentational** and **Container** components.

**Presentational** components are only concerned with the *looks* of the application and they can be divided in 2 types. **Logical** components can manage their own state which won´t be used in any other place of the application. **Pure** components are stateless functions that are only concerned in the *looks* of the app.

**Container** components manage how all the **Presentational** Components work together. These components interact with the Application State and propagate data to all the **Presentational** Components. 

### Action Types

An action type in Redux represents an explicit action type that will occur within the application. It is expressed in JavaScript as a string constant. 

For simplicity, we will be building a simple TODO app where you can ADD, REMOVE, UPDATE and COMPLETE. We will need to create one action type for each of the actions mentioned before.

It is best practice to create a separate directory to contain all of our action types. Go ahead and create `todos.js` inside actiontypes directory.

```bash
src
|- actiontypes
	|- todos.js
```

Now we can edit `todos.js` and add all our Action Types.

```javascript
export const ADD_TODO = 'todo/ADD_TODO';
export const REMOVE_TODO = 'todo/REMOVE_TODO';
export const UPDATE_TODO= 'todo/UPDATE_TODO';
export const COMPLETE_TODO= 'todo/COMPLETE_TODO';
```

As mentioned before, Action Types are implemented in JavaScript as string constants describing what will happen to the state.

### Reducers

Reducers are pure functions that take 2 arguments, the current state and the action being taken. They produce a new state depending on the action they are passed. 

First let us create a directory to store all the reducers.

```bash
src
|- actiontypes
|- reducers
	|- todos.js
```

Now we can add the reducer for our TODOs.

```javascript
import * as TodoActionTypes from '../actiontypes/todos';

initialState = [
  {
    title: 'Buy beer',
    isCompleted: false
  },
  {
    title: 'Prepare some coffee',
    isCompleted: false
  },
  {
    title: 'Write an article about Redux',
    isCompleted: false
  }
];

export default function Todo(state=initialState, action) {
  switch(action.type) {
    // ADD a TODO
    case TodoActionTypes.ADD_TODO:
    return [
      ...state,
      {
        title: action.title,
        isCompleted: false
      }
    ];
    // REMOVE a TODO
    case TodoActionTypes.REMOVE_TODO:
    return state.filter((todo,index) => index !== action.index);
    // UPDATE a TODO
    case TodoActionTypes.UPDATE_TODO:
    return state.map((todo, index) => {
      if(index === action.index) {
        return {
          title: action.title,
          isCompleted: todo.isCompleted
        };
      }
      return todo;
    });
    // COMPLETE a TODO
    case TodoActionTypes.COMPLETE_TODO:
    return state.map((todo, index) => {
      if(index === action.index) {
        return {
          title: todo.title,
          isCompleted: true
        };
      }
      return todo;
    });

    default:
    return state;
  }
}
```

This seems a lot but don't worry ill explain it by parts.

* The **first** thing to notice is that we imported all the Action Types. We need those to know what action will be done by the reducer.

* The **second** part is that object named `initialState`. If you have some initializer state data, this is the right place to add it. When the app starts, the initial state of the app will be set by the reducer. Notice we pass the initialState as a default parameter to the `Todo` reducer.

* In the **last** part, we define the reducer, adding a `switch` block that will compare the passed `action.type` with the different Action Types contained in `TodoActionTypes`.
Depending on the `action.type`, a new state is generated. 


### Actions

Actions are objects containing information about the Action Type and what needs to change in the state. Actions are generated by Action Creators, functions which will be triggered by the App as we will see later.

First let us create a directory to store all the actions.

```bash
src
|- actiontypes
|- reducers
|- actions
	|- todos.js
```

Let us write our Action Creators:

```javascript
import * as TodoActionTypes from '../actiontypes/todos';

export const addTodo = title => {
  return {
    type: TodoActionTypes.ADD_TODO,
    name
  };
};

export const removeTodo = index => {
  return {
    type: TodoActionTypes.REMOVE_TODO,
    index
  };
};

export const updateTodo = (index, title) => {
  return {
    type: TodoActionTypes.UPDATE_TODO,
    index,
    name
  };
};

export const completeTodo = index => {
  return {
    type: TodoActionTypes.COMPLETE_TODO,
    name
  };
};
```

These action creators will be triggered by the application and then dispatched to the store to make changes on the state. As you can see we created 4 Action Creators, 1 for each Action Type.


### The Store

The store is the primary source of information (single source of truth) of the application. The store interacts with the App itself via a Provider. The App containers will subscribe changes to the Provider which is in charge of dispatching this changes to the Store. 

In order to wire our application to all the code we have written until now we need the following:

* **Provider:** A special component provided by `react-redux`
* **createStore:** A function that let us create the store. Provided by `redux`
* **Reducers:** We will need to pass the reducers to the `createStore` function.

Lets wire everything in the render file of our app, `./src/index.js`:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import TodoReducers from './src/reducers/todos';

import TodoApp from './src/containers/TodoApp';

const store = createStore (
  TodoReducers
);

ReactDOM.render (
  <Provider store={ store }>
    <TodoApp />
  </Provider>,
  document.getElementById('root')
);
```

In the last piece of code we made our imports, created the store passing the reducers, and rendered the App wrapping it with a Provider component. Note that we passed the created store as props to the Provider so it will be available to the container `TodoApp`.


### Binding Action Creators

There are 2 more things to do for our app to work. We need to connect the store with the container `TodoApp` and bind the Action Creators to the app.

We connect the app with the store with the `connect` function from `react-redux`. In addition, we will need to create a function that will map the state into props for the container to use.

Lets see how this works:

```javascript
...
import { connect } from 'react-redux';


class TodoApp extends Component {
  render(
    ...
  )
}
TodoApp.propTypes = {
  todos: PropTypes.array.isRequired
}

const mapStateToProps = state => (
  {
    todos: state
  }
);

export default connect(mapStateToProps)(TodoApp);

```

In the last code snippet we subscribed the `TodoApp` container to any state updates with the `connect` function.

The last thing we need is to bind the action creators in the `TodoApp` container. This is done via a function called `bindActionCreators`, provided by `redux`. See code snippet below:

```javascript
...
import { bindActionCreators } from 'redux';
import * as TodoActionCreators from '../actions/todos';

class TodoApp extends Component {
  render(
    const { dispatch, todos } = this.props;
	
	// Create Bound Action Creators
    const addTodo = bindActionCreators(TodoActionCreators.addTodo, dispatch);
    const removeTodo = bindActionCreators(TodoActionCreators.removeTodo, dispatch);
    const updateTodo = bindActionCreators(TodoActionCreators.updateTodo, dispatch);
    const completeTodo = bindActionCreators(TodoActionCreators.completeTodo, dispatch);

    const todoComponents = todos.map((todo, index) => {
      return (
        <div>
          <h2>todo.title</h2>
          <CompleteCheckBox completeTodo={completeTodo} isCompleted={todo.isCompleted} />
          <Button updateTodo={updateTodo} />
          <Button removeTodo={removeTodo} />
        </div>
      );
    });

    return (
      <div>
        <h1>Todo App</h1>
        <AddTodoForm addTodo={addTodo} />
        <div className="todos">
          { todoComplenents }
        </div>
      </div>
    );
  )
}
...
```
That is all! We have now a wired app with a redux store. 

This was just a simple demonstration on how you can add redux to a new or existing project. After you understand this concepts you will see the benefits when your app starts to scale. 

### Conclusion

In this article we learned how to set up redux with a React app. We when through the following aspects:

* Action Types are string constants that describe what will happen to the state.
* Reducers are pure functions that receive as parameters the current state and an action. They produce a new state.
* Actions are objects containing information about the Action Type and what needs to be changed in the state
* The store is the single source of truth of the application and it is provided to the containers that need to subscribe changes.
* The app subscribes changes via Bound Action Creators that will automatically dispatch the action to the store.

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!