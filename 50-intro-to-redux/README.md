# Intro to Redux

- What's hard about managing state in React
- Introducing Redux
- Designing our redux state
- Dispatching actions to our store
- Updating based on actions
- Problems with this version of our redux app

## The Problem - Refactoring Components
![gif](http://g.recordit.co/s2OFw0VtYb.gif)

We've changed `App` so that the computed value can render in the Header. What did we have to change?

- state initialization moved to App
- state managing functions moved to App
- count in Counter changed to `this.props.count`
- computation moved to Header
- computation switched to use a prop
- change functions switched to props in Counter

Almost everything in the code for the App had to change to accomodate this tiny design change!

## Introducing Redux - The Plumbing

Installing redux: `yarn add redux`

Creating our redux store:

```js
import { createStore } from 'redux'
const reducer = (oldState, action) => {
  if(oldState === undefined) {
    return {}
  }
  return oldState;
}

const store = createStore(reducer);
```

## Designing our state

- Starting to think in Redux
- 'Shape' of our state: keys in our store and the _types_ of the values
- e.g. "`count` will be a number. `friends` will be an array of Friend objects. `loading` will be a boolean.

> Note: This is just like step 3 of Thinking in React. We _just_ need to figure out the shape of the state, not where it lives.

- What should the initial state be?
- Redux init action - `"@@redux/INITxyz"`
- Warning on returning `undefined` from our store

### Reading from the store

We want to read the count from the store
We can get the current state with `getState`

```js
// instead of this.props.count
store.getState().count
```

**We no longer depend on props!**

## Dispatching Actions to our store

- What are the things that can happen in our app?
- These will become the _actions_ that our store responds to

Action: A plain old javascript object (POJO) with the key `type`, a string. Optionally, more data.

```js
{ type: 'CLICKED_PLUS' }
```

```js
increment = () => {
  store.dispatch({type: 'CLICKED_PLUS'})
}


decrement = () => {
  store.dispatch({type: 'CLICKED_MINUS'})
}
```

Q: Where should these functions live?
A: Wherever you like! They don't depend on `setState`, so they can be defined in the component where they are used.

## Updating State

Let's see the actions flow through our reducer:

```js
const reducer = (oldState = { count: 0 }, action) => {
  console.log('action', action)
  return oldState;
}
```

*Rule: we must not mutate the old state!*

Our reducer should return a _new_ object with the updated state

```js
const reducer = (oldState = { count: 0 }, action) => {
  console.log('action', action)
  if(action.type === 'CLICKED_PLUS') {
    return { count: oldState.count + 1 }
  }
  return oldState;
}
```

If we expect to return different states based on different actions, we can use a switch statement:

```js
const reducer = (oldState = { count: 0 }, action) => {
  console.log('action', action)
  switch(action.type) {
    case 'CLICKED_PLUS':
      return { count: oldState.count + 1 }
    case 'CLICKED_MINUS':
      return { count: oldState.count - 1 }
    default:
      return oldState;
  }
}
```

### Why isn't our view updating?

The redux store is changing! (We can see if we log it)

React only rerenders on props or state change. We need a little hack to make our store updates rerender our app.

```js
componentDidMount() {
  store.subscribe(() => this.forceUpdate())
}
```

## Challenge

Add buttons, actions, and cases to your reducer so that we can increment and decrement by `+3` and `-5`

## Problems to fix
<!-- one big file (like to have many files with clear responsiblities) -->
- how do we access store.getState and store.dispatch if we don't have a store variable?
- get rid of
```
  componentDidMount() {
    store.subscribe(() => {
      this.forceUpdate();
    });
  }
```
- state with many keys - like it to be _simpler_
- same action in lots of places, mispelling becomes easy
- async
- what does this look like in a bigger app?


## `react-redux` Provider and Connect

- refactor into files
- install react-redux
- introduce the Provider
- `connect`ing our components
- `mapStateToProps`
- `mapDispatchToProps`


## What should get its own file?
- reducer
- store
- App
- Header
- Counter


## Higher Order Components and Connect
// withRouter
// wrap a component in it
// gave that component the  { history, location, match } as props
// <Route component={MyComponent} />
// <Route render={(props) => MyComponent } />
// MyWrappedComponent = withRouter(MyComponent)

// withRouter is a "Higher Order Component"
// function (Component) => Component

// connect(configuration) => (Component) => WrappedComponent
