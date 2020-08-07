# React Hooks
* This set of notes details how to use state, lifecycle features, context and other features previously only accessible for class components inside regular stateless functional components
* React hooks are special functions that allow you to hook into the power of class components with a cleaner syntax
* Hooks allow you to: 
    * Manage state and rendering of UI when state changes 
    * Provide access to your app's context
    * Update components with an alternative to lifecycle methods 
* These notes cover `useState`, `useEffect` and `useContext` hooks 
* Hooks are available starting with React 16.8 and they are currently opt-in meaning you don't have to use them (yet) but you can choose to
* Hooks are only available on function components in React

## `useState`
* [docs](https://reactjs.org/docs/hooks-reference.html#usestate)
* Hooks are functions that allow you to "hook into" something useful 
* The `useState` React hook allows you to use React state in a function component, where normally you would have only access to state on a class component
* `useState()` is the equivalent of both `this.state` and `this.setState` on class components
* To use the hook, you need to import it from React then define a variable for the state of the function component:

    ```js 
    import React, { useState } from 'react'
    const [ score, setScore ] = useState(0)

    function App() {...}
    ```
* `useState` accepts one optional argument which is the initial value of the state variable 
* calling `useState()` returns an array containing the value of the state variable and a function to update the state variable
* You use destructuring assignment to get the value and the function, as seen above
* In class components, state must always be reresented with an object, but with `useState()` it can be an object, array or any other JavaScript primitive
* Once you have the state variable set, you can use it anywhere just as you would use any other variable
* To update the state variable, simply call the updater function you defined and pass it the new value: 

    ```js 
    function App() {
        const [score, setScore] = useState(0);

        return (
            <h1>{ score }</h1>
            <button onClick={() => setScore(score + 1)}>
            Increase score
            </button>
        );
    }
    ```
* If you need to update your state variable based on the previous state value, you can pass a callback to your updater function that takes the previous state as an argument: 

    ```js 
    return(
        <h1>{ message }</h1>
        <h2>{ score }</h2>
        <button onClick={() => setScore(prevScore => prevScore + 1)}>
            Increase score
        </button>
    );
    ```

## `useEffect` 
* [docs](https://reactjs.org/docs/hooks-reference.html#useeffect)
* React lifecycle methods allow you to attach behaviour, or "side effects" to different phases of a component's lifecycle, such an mounting, updating, and unmounting 
* Side effects performed during lifecycle changes can include re-rendering a component, updating the DOM when data changes, fetching data from an API, performing cleanup when a component unmounts, etc
* Previously you could only perform side effects on life cycle changes with class components because you could only use lifecycle methods on class components, but `useEffect()` allows you to do this with function components
* `useEffect()` is like `componentDidMount`, `componentDidUpdate` and `componentWillUnmount` all rolled into one
* To use, import `useEffect` from React, then call it in your function component passing in a callback that performs the desired side effect:

    ```js 
    import React, { useEffect } from 'react'

    function App() {
      useEffect(() => {
        console.log('useEffect called')
      })
    }
    ```
* The `useEffect` hook executes the callback immediately when the component renders. To run it only once when the component first renders and not again after that, pass in an optional array after the callback: 

    ```js 
    function App() {
      useEffect(() => {
        console.log('useEffect called')
      }, [])
    }
    ```
* The second optional array argument instructs React to skip applying the side effect and re-rendering if the values in that array have not changed between re-renders 
* To run `useEffect` only when data in your component changes, you can pass **dependencies** to the array, which would be variables for any state data that are used or updated by `useEffect`. The hook will then check if any of the dependencies have changed and perform the effect if so
* Any variables used in `useEffect` *must* be listed as dependencies in the hook
* `useEffect` is often used to fetch data from an API

* [Demonstration of React hooks with a simple app](https://teamtreehouse.com/library/create-a-gif-search-app-with-react-hooks)

## `useContext`
* [docs](https://reactjs.org/docs/hooks-reference.html#usecontext)
* The `useContext` hook provides all the functionality of React's Context API with a single function
* To use `useContext` first import it from React: 

    ```js
    import React, { useContext } from 'react'
    ```
* Unlike the Context API, with `useContext` you do not need a `Consumer` component to subscribe to a `Provider`. Instead, you create context with the `React.createContext` method and then access that context directly in your function component: 

    ```js 
    import React, { useContext } from 'react';
    import { ScoreboardContext } from './Context';

    const Stats = () => {
      const { players } = useContext(ScoreboardContext);
    }
    ```
* You can destructure and use any data or functions contained within the `Provider` by importing the context variable and passing it as an argument to `useContext()`
* Accessing props this way via context with `useContext` creates a subscription to that context, and the component will automatically re-render whenever the context value it subscribes to changes
* There is no need to wrap the component's `return` statement in a `Consumer` with a render prop, making your code a lot cleaner and easier to understand

## Going further 
* React provides [additional hooks](https://reactjs.org/docs/hooks-reference.html#additional-hooks) beyond what is covered in these cursory notes. As always, read the docs!
* React also provides the ability to [write custom hooks](https://reactjs.org/docs/hooks-custom.html)
* A few rules: 
    * Call hooks only at the top level of a React function component 
        - don't call hooks inside loops, conditions or nested functions
    * Only call hooks inside React function components, never in methods on class components