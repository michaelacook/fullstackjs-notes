# React Components 
* This set of notes builds on [React Basics](react-basics.md) and introduces common usages, best practices and design patterns for building well-written and modular components

## Unidirectional data flow
* It is generally optimal in React to have a single top-level component that centrally manages all application data
* Data should only move down the component hierarchy in a single direction via props 
* When data is distributed throughout an application's components, it is most difficult and complicated, and often just impossible for other components that require access to that data to get access, since each component's data is private
* As a rule, when two or more components need access to the same data, their common parent should store that data, making it accessible to both. In practice this means almost all data will live in application state in the root component
* While data should only flow down, it is possible to pass down methods from the root component that will update state via props
* As long as these methods are properly bound to the root component's instance at runtime, child components can call a reference to a parent component's method and update application state

## Controlled components 
* [Controlled Component docs](https://reactjs.org/docs/forms.html#controlled-components)
* Though it is a best design practice to maintain state only on the root component as much as possible, forms are common and are by nature stateful
* Form elements generally maintain a state and update that state based on user input 
* In React, it is generally best to maintain the state of a form element in it's component state and update the value of the form element based on component state
* This is called a "controlled component"
* Instead of the value of a form element changing directly based on user input, the element listens for change, and on change updates the component's state
* The component's state is used as the "single source of truth" and sets the value of the form element 
* E.g:

```js
import React, { Component } from 'react'

class Form extends Component {
    constructor(props) {
        super(props);
        this.state = {
            input: ""
        }
    }

    handleInputChange = event => {
        this.setState({
            input: event.target.value
        });
    }

    render() {
        return(
            <form>
                <input 
                  type="text"
                  onChange={() => this.handleInputChange(e)}
                  value={this.state.input}
                >
            </form>
        )
    }
}

export default Form
```

* The above example shows how a basic controlled component works
* To create a controlled component:
    1. Initialize state for the value of the input
    2. Listen for change on the input to detect when the value is updated
    3. Create an event handler that updates the value state
* onChange is the best event listener to use for this

## Stateful components and lifecycle methods 
* [State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html)
* Components have a lifecycle: they are created and mounted to the DOM, change their data over time, and are then unmounted and destroyed
* React has certain methods that are called lifecycle methods, because they are called at each point in the component's lifecycle
* When a component is mounted, the method `componentDidMount` is called; when a component is re-rendered, the method `componentDidUpdate` is called, and when a component is just about to be unmounted the method `componentWillUnmount` is called 
* These methods can be used as **hooks** to attach code to
* For instance, you will commonly make an API request when a component is mounted, or you will want to kill unfulfilled network requests or cancel timers before a component is unmounted, otherwise those pending requests or timers will continue after the component is unmounted causing memory leaks 

## Common design patterns 
* There are certain component design patterns that will optimize your code and make it easier to understand and maintain. This section will discuss some useful and commonly-implemented patterns in React

### Pure Components 
* [Pure Components docs](https://reactjs.org/docs/react-api.html#reactpurecomponent)
* Whenever state changes in a React application, all the children of the component whose data changed will re-render 
* However, not every single child component may actually need to re-render
* Consider a root component that renders a list of sibling elements. When data changes, some of the list elements may receive different props, but not all of them; yet they will all re-render
* In a situation like this, you may want to extend the `PureComponent` class instead of the `Component` class
* `PureComponent` works basically the same way as `Component`, but will only re-render when it's props or state is different
* E.g:

```js 
import React, { PureComponent } from "react"
import PropTypes from "prop-types"
import Counter from "./Counter"
import Icon from "./Icon"

class Player extends PureComponent {
  constructor(props) {
      super(props)
  }

  static propTypes = {
    hasHighest: PropTypes.bool,
    changeScore: PropTypes.func,
    removePlayer: PropTypes.func,
    name: PropTypes.string.isRequired,
    score: PropTypes.number,
    id: PropTypes.number,
    index: PropTypes.number,
  }

  render() {
    const {
      hasHighest,
      removePlayer,
      changeScore,
      name,
      index,
      score,
      id,
    } = this.props
    return (
      <div className="player">
        <span className="player-name">
          <button className="remove-player" onClick={() => removePlayer(id)}>
            ✖
          </button>
          <Icon highScore={hasHighest ? true : null} />
          {name}
        </span>

        <Counter index={index} score={score} changeScore={changeScore} />
      </div>
    )
  }
}

export default Player
```
* If a component's `render()` method renders the same result given the same props and state, you can use `PureComponent`
* behind the scene, PureComponent implements a method called `shouldComponentUpdate()`which    runs a shallow comparison of state and props and determines and only re-renders the component and it's children if state or props have actually changed, otherwise it prevents a re-render
* However, because this comparison is shallow and not deep, you should not extend `PureComponent` for any components that use deeply nested data structures or the comparison performed by `shouldComponentUpdate()` could lead to a false negative
* In larger applications using pure components can mean a performance increase
* However, any child components of a pure component should also be pure components as well

### Destructuring props
* Destructuring data out of props is a common pattern used by React developers to keep code clean and avoid the repetitive use of `this.[propName]`
* With function components, it is best to destructure props inside the function parameters 
* E.g:

```js 
const Counter = ({ index, score, changeScore }) => {
  return (
    <div className="counter">
      <button
        onClick={() => changeScore(index, -1)}
        className="counter-action decrement"
      >
        {" "}
        -{" "}
      </button>
      <span className="counter-score">{score}</span>
      <button
        onClick={() => changeScore(index, +1)}
        className="counter-action increment"
      >
        {" "}
        +{" "}
      </button>
    </div>
  )
}
```
* This is a very clean implementation and it also tells other developers exactly what props your component uses right at the top
* For class components, you can destructure props inside your `render()` method
* E.g: 

```js 
render() {
    const {
      hasHighest,
      removePlayer,
      changeScore,
      name,
      index,
      score,
      id,
    } = this.props
    return (
      <div className="player">
        <span className="player-name">
          <button className="remove-player" onClick={() => removePlayer(id)}>
            ✖
          </button>
          <Icon highScore={hasHighest ? true : null} />
          {name}
        </span>

        <Counter index={index} score={score} changeScore={changeScore} />
      </div>
    )
}
```
* When destructuring props in `render()` make sure not to destructure inside the `return` block

### Refs and the DOM
* [Docs for refs](https://reactjs.org/docs/refs-and-the-dom.html)
* [refs glossary entry](https://reactjs.org/docs/glossary.html#refs)
* In React, elements and components are generally not modified directly. Rather, you pass them new props, and they re-render. But some times you need a reference to a component's instance or the underlying DOM node of a component. For this React provides **refs**
* Refs are usually used for managing focus, text selection and triggering imperative animations
* Refs can also be used instead of controlled components for forms, but this is not recommended unless you have a lot of form elements and they do not need real-time validation
* To create a ref you use the method `React.createRef()` and assign the return value to a variable 
* You can then pass that ref to the `ref` attribute on an element 
* E.g: 

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```
* The above example is from the React docs
* You can then access the element's DOM node or instance with the syntax `refVariable.current` allowing you to get it's value or change it's styling
* Use refs sparingly. React is intended to be declarative for the most part
* Read the docs for a more in-depth discussion of refs

### Validate props with PropTypes 
* reading: 
  - https://reactjs.org/docs/static-type-checking.html
  - https://www.typescriptlang.org/
  - https://flow.org/
  - https://reactjs.org/docs/typechecking-with-proptypes.html
  - https://reactjs.org/docs/typechecking-with-proptypes.html#proptypes
  - https://www.npmjs.com/package/prop-types
* PropTypes is a library that used to come with React, but was taken out to allow more developer choice
* PropTypes is a great way to validate the data passed via props to your components without using TypeScript 
* as an app gets bigger, it's a good practice to validate your props or "type check" in 
    order to make sure your component is receiving the correct data types in props 
* this helps avoid bugs and it also lets other developers who may be working on the 
    same code base know exactly what type of data your component needs to receive 
* three popular ways to type-check in React: 
  1. PropTypes
  2. TypeScript 
  3. Flow
* Using PropTypes allows you to catch bugs quickly because you will get console warnings when invalid data is being passed to a component 
* PropTypes also acts as documentation for your components, telling other developers exactly what your component expects to receive
* To set up PropTypes:
  1. run `$ npm install prop-types --save`
  2. `import PropTypes from 'prop-types;'
  3. Add an object property to your component that sets prop values as keys and their validators as values to those keys
* E.g: 

```js
import React from "react"
import PropTypes, { arrayOf } from "prop-types"

const Stats = ({ players }) => {
  const totalPlayers = players.length,
    totalPoints = players.reduce((sum, curr) => {
      return sum + curr.score
    }, 0)

  return (
    <table className="stats">
      <tbody>
        <tr>
          <td>Players:</td>
          <td>{totalPlayers}</td>
        </tr>
        <tr>
          <td>Total Points:</td>
          <td>{totalPoints}</td>
        </tr>
      </tbody>
    </table>
  )
}

// propTypes validators
Stats.propTypes = {
  players: arrayOf(
    PropTypes.shape({
      score: PropTypes.number,
    })
  ),
}
```

### Static PropTypes and default props
* When writing propType validators for class components you can specify your propTypes as a static property on your class
* This puts the propTypes at the top of the file instead of the bottom and means your component doesn't require an instance to be validated
* E.g: 

```js 
class Player extends PureComponent {
  // chaining isRequired to a propType makes the prop required
  static propTypes = {
    hasHighest: PropTypes.bool,
    changeScore: PropTypes.func,
    removePlayer: PropTypes.func,
    name: PropTypes.string.isRequired,
    score: PropTypes.number,
    id: PropTypes.number,
    index: PropTypes.number,
  }

  render() {
    // this is the best way to destructure props inside a class component
    const {
      hasHighest,
      removePlayer,
      changeScore,
      name,
      index,
      score,
      id,
    } = this.props
    return (
      <div className="player">
        <span className="player-name">
          <button className="remove-player" onClick={() => removePlayer(id)}>
            ✖
          </button>
          <Icon highScore={hasHighest ? true : null} />
          {name}
        </span>

        <Counter index={index} score={score} changeScore={changeScore} />
      </div>
    )
  }
}
```
* You can make a prop required by chaining `.isRequired` to the propType validator
* You can also add default props for a component that are automatically passed to it when the application does not pass any props 
* E.g: 

```js
const Icon = ({ highScore }) => {
  return (
    ...
  )
}

Icon.propTypes = {
  isHighScore: PropTypes.bool,
}

// default props
Icon.defaultProps = {
  isHighScore: null,
}
```

## Going further 
* All the notes in this note set are basic. These notes are only meant to be a starting point for understanding aspects of component design. To understand implementations of these features and patterns in greater detail you should read the React documentation.