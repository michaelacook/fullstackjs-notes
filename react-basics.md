# React.js Basics 
* A few good articles to read:
    * [Declarative vs. Imperative Programming](https://codeburst.io/declarative-vs-imperative-programming-a8a7c93d9ad2#:~:text=Declarative%20programming%20is%20a%20programming,that%20change%20a%20program's%20state.&text=Declarative%20Programming%20is%20like%20asking%20your%20friend%20to%20draw%20a%20landscape.)
    * [Thinking in React](https://reactjs.org/docs/thinking-in-react.html)
    * [The Public Folder `in create-react-app`](https://medium.com/@jenniferdobak/the-public-folder-and-favicons-in-create-react-app-8dc2cc1d492b)
    * https://medium.com/@bretdoucette/understanding-this-setstate-name-value-a5ef7b4ea2b4
    * [Deploying a React app to Github Pages](https://dev.to/yuribenjamin/how-to-deploy-react-app-in-github-pages-2a1f)
* This noteset assumes you're using React for the web, not React Native

## What is React?
* React is a library for building UIs in JavaScript
* React allows you to break your application into separate pure components that automatically 
re-render whenever the state of the application changes
* In React, the front-end layer of your application is broken into individual components that may or may not have their own data, and may or may not receive data from a parent component. These components can only alter their own native data, and cannot alter any data they receive. They are pure components that have no side-effects in your program
* In React, your application is a function of it's data. As data changes, React automatically updates your application's components
* Typically, React applications have a single root-level component in which all other components are nested, but you can use as much or as little React as you want
* React can be used to build client applications for the web, desktop (with Electron.js) and mobile with React Native, a version of React for developing cross-platform mobile apps
* React is split into several main components: React (the main library) and ReactDOM which has APIs for the web
* React is extremely popular and was created by Facebook engineers

## Setup
* The fastest way to get a React application setup is with the tool called Create React App
* One directory up from where you want your application, run

`$ npx create-react-app my-app-name`

* This will install React, webpack and other dependencies to give you a full development and build system
* You can then run `$ npm start` to start up the development server 
* In development, React must run through a local development server
* The app will come with boilerplate in the `src` and `public` directories. You can delete the boilerplate in `src` but be careful not to delete `index.html` in `public`
* When using Create React App all the JavaScript and CSS for your project lives in `src`. Webpack never touches your public folder, which holds `index.html` and any favicons the app uses
* Next, run `$ touch index.js` to create your index file. This file will pull in all your components and render the the root level component of your app
* In `index.js` you have to require React and ReactDOM. It should look like this: 

```js
/* app entry point */

import React from 'react';
import ReactDOM from 'react-dom';
```
* The root level component should be imported into the index file to be rendered
* In React, the root level component is "mounted" to an html element, typically a `div` with an id attribute of "root". React does not require the element to be called "root" but it is customary to do so
* So assuming your root level component is called `App` (note: components are title-cased), the rest of your index page should look like this: 

```js
... 
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```
* The `ReactDOM.render` method takes two arguments: the root level component, and the element it is being mounted to
* You can now begin building your app's components

## Building components
* Components can be any meaningful part of your application. They can be as big or small as you want or as makes sense for your project
* Examples of components could be a weather widget, a comment field, a button, etc.
* Components can take the form of an ES6 class, or a function
* Class components are usually used when the component needs to store data or methods 
* Function components are usually used when you need a UI component that stores no data of its own
* Components can also be **composed** from combinations of other components
    ### A word about JSX
    * JSX is an XML-like markup used to declare components in React
    * It looks like HTML, but it isn't: it's all JavaScript under the hood
    * Therefore, when writing JSX you need to use camel-case for a number of html attributes 
    * E.g `className` instead of `class`
    * You can write any legal JavaScript expression inside curly braces inside a JSX expression
    * E.g: 
    ```js
    <Button value={eval("5*5")} />
    <span>{45 / 5}</span>
    ```
* Let's create a root level component called `App`:

```js
import React, { Component } from 'react';

class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            // this is JSX. The component isn't really returning html, it's returning a JS object
            <div>Hello, world!</div>
        );
    }
}

export default App;
```
* First, you import React, and if you're going to write your component as a class, you import the `Component` class as well 
* All class components **must** extend the `Component` class
* (In object-oriented programming, classes can "extend" or inherit from one another. A child class may extend from a parent class, so it receives all the methods and properties of the parent and then defines its own unique properties and methods. the `super` function called in the constructor is what adds all the parent properties and methods to the child and must be called first in a child class)
* Constructors are optional in the latest version of React, and should actually only be used if you're setting properties on a component class. But if you are, the constructor must have the parameter `props` and call `super` on `props` before anything else 
* The only method a class component **must** have is `render`, which returns the component

## Function components
* You will likely want your app to have more than one component. Let's give `App` a child component that displays a header
* Make a new file called `Header.js` in the `src` directory `$ touch Header.js`
* Inside the file import React:

```js
import React from 'react';
```
* and create a function called `Header`:

```js
...
const Header = () => {
    return (
        <header className="header">React.js Notes</header>
    );
}
```
* (note: the returned component is inside parentheses. This is not necessary with this component, but when your component spans multiple lines it is necessary to surround it in parentheses because by default the `return` keyword will return only the first line)
* The `Header` component can now be imported into the `App.js` file and added as a child component to `App`:

```js 
// inside App.js
import React, { Component } from 'react';
import ReactDOM from 'react-dom';
import Header from './Header';

class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            <Header />
            <div>Hello, world!</div>
        );
    }
}
```
* The `Header` component is imported and then declared with a very similar syntax to HTML. Under the hood, declaring your component this way is just a function call to return the component
* You can nest as many components inside the root component as you want, and likewise you can also nest child components inside other child components to make as complex of a UI as your project calls for
* Ultimately all components are rendered when root level component is mounted and rendered in the DOM with the `ReactDOM.render` method in your index file

## Dealing with data
* The whole point of a dynamic web application is that is it dynamic, not static. You will rarely if never want to simply render static components with only hard-coded data
* React has two ways of handling data: `state` and `props` (short for "properties")
* `state` is a regular JavaScript object that can be defined as a property of a class component. When you want your component to handle dynamic data, that data will live in `state`
* Data in `state` will of course change over time depending on API or database calls and/or user interaction. When data in `state` changes, React automatically re-renders the component whose state changed, as well as all child components of that component 
* `props` represent data that are passed down from a stateful parent component to a child component
* Your application will probably have only one or a few class components that actually maintain their own state, and other child components will receive their data from the parent in the form of `props`
* Likewise, class components that are children of other class components can also receive props from a parent
* Let's see this in action by re-writing `Header` to accept a `props` parameter and display whatever it recieves from `App`:

```js
...
const Header = props => {
    return (
        <header className="header">{props.headerText}</header>
    );
}
```
* The `Header` component will now receives `props` and will display the `headerText` property, but first we have to actually pass props from the `App` component to the child `Header`:

```js
...

class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            // here we add props data
            <Header headerText={"React.js Notes"} />
            <div>Hello, world!</div>
        );
    }
}
```
* To pass props to any child component, you use the above syntax. More formally:

```js
<Component propsName={propsData} />
```
* You can give any name you want to the props you pass from parent to child component, but it's best to apply the same principles you would use to come up with a regular variable name. Once you pass the props into the child component, the data is available in the child via `props.propsName`
* However, if you are passing `props` to a child class component, the props are accessible via `this.props.propsName`
* A component receiving `props` can never modify the data it receives. `props` is immutable. Only the parent component that "owns" the data in its `state` object can modify that data 
* However, it is possible to pass class methods that alter state from the parent component down to the child via `props` and call those functions when the user interacts with the child component in some way
* Example of passing a method to a child via `props`:

```js
class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            name: ""
        }
    }

    foo = () => {
        this.setState({ name: "Henry" });
    }

    render() {
        return (
            // here we add props data
            <Header headerText={"React.js Notes"} foo={this.foo} />
            <div>Hello, world!</div>
        );
    }
}
 
...
// inside Header.js

const Header = props => {
    return (
        <header onClick={props.foo} className="header">{props.headerText}</header>
    );
}
```
* Now when the user clicks on the header elent on the page, the name property of `App`s `state` property will be set to "Henry"
* We create the method in the parent component and pass a reference to that method via props down to the child. The child can then either call that method immediately on render by actually calling the method instead of accepting a reference to it, or the component can call the method as a callback to an event listener
* (note: event listeners look similar to inline event listeners in HTML except that they use camel case. No need to use the native DOM method `addEventListener`. You add the event listener directly on the component. This may feel strange at first since adding inline event listeners is not a good practice, but it is the standard way in React)
* Recap: even though child components cannot alter data they are passed via `props`, you can pass methods to child components from the parent class that do manipulate data. These methods can manipulate data because they belong to the **stateful** parent class, not the child class. The child class only calls the method, but it does not own it. It is common for a stateful class component to pass methods down to child function components

### Binding
* By default, methods are not bound to objects in JavaScript
* This means that when a method is defined on an object, the context in which the function executes changes depending on where and how the method is called 
* E.g:

```js
const foo = {
    name: "Efe",
    sayHello: function() {
        console.log(`Hello, my name is ${this.name}`);
    }
}

const bar = foo.sayHello;
bar();
Hello, my name is 
```
* In the example above, the `foo.sayHello` method gets assigned to a new context, and therefore since it isn't bound by default to its parent object `foo`, it's reference to `this.name` becomes undefined. `this.name` no longer refers to `foo` but to the new lexical context, and since `this.name` isn't defined in the new context, nothing prints out
* This matters when passing methods down from stateful components to child components, because methods that are not bound to the parent component will take on the `this` context of the child component they are called in unless they are properly bound to the parent component. Let's see an example of an unbound method passed to a child:

```js
class Example extends Component {
    constructor(props) {
        super(props);
        this.state = {
            name: "example"
        }
    }

    sayHello() {
        alert(`Hi, my name is ${this.name}`);
    }

    render() {
        return (
            <Child sayHello={this.sayHello} />
        )
    }
}

const Child = props => {
    return <button onClick={props.sayHello}>Press Me</button>
}
```

* The above example will lead to an error in the console. The `sayHello` method accesses the `Example` state property `name`, but when a reference is passed to the `Child` component, `this` refers to `Child` and not `Example`. So `this.name` will be `undefined` and React will throw an error and stop running
* However, there are a number of ways to bind methods to their parent:
1. `bind()`
    * You can find a method to an object's context by calling the `bind()` function on it
    * `bind()` returns a new function that is bound to the context that it is passed as an argument
    * New to ES6
    * You can bind the method when you pass it as a reference to a child component through props: 
    ```js 
    <ChildComponent onClick={this.methodName.bind(this)}>
    ```
    * You can bind the method in the class constructor: 
    ```js 
    constructor(props) {
        super(props);
        this.methodName = this.methodName.bind(this);
    }
    ```
2. Calling the method rather than passing a reference to it
    * You can also avoid binding issues by calling the method in an event handler rather than passing a reference to the method into the event handler. Instead of writing this: 
    ```js 
    onClick={this.methodName}
    ```
    * Write this: 
    ```js 
    onClick={() => this.methodName()}
    ```
    * If you use this option, the method must be called inside an anonymous function, else the method will be called when the app is mounted to the DOM and not when the event handler fires 
    * This works because anonymous functions take on the context of their parent function,
    so if you do this inside the render method, it will receive the `this` context of the object when an instance of it is made
3. Arrow functions
    * Arrow functions have lexical scoping, meaning they do not create a new `this` context but take on the `this` context of their parent by default, so arrow functions are bound
    * Instead of defining your methods like this: 
    ```js 
    methodName() {
        // body
    }
    ```
    * Define your method with an arrow function:
    ```js
    methodName = () => {
        // body
    }
    ```
    * This is the simplest solution to method binding in my opinion

## Keys 
* Often in React you will want to render lists of sibling elements, whether actual `<ol>` or `<ul>` elements or collections of `<div>` elements
* React needs a way to distinguish between sibling elements when they are going to change in any way
* Keys are a unique identifier that gives React a way to distinguish siblings 
* Keys can an index generated from a loop, but this is discouraged as it can lead to problems 
* When getting data from an API or database call, some kind of unique identifier often comes along with the data, and that is what can be used as the key 
* Syntax:

```js 
<ul>
    <li key={1}>
    <li key={2}>
    <li key={3}>
</ul>
```

## Mutable and immutable data
* [Why Immutability is Important](https://reactjs.org/tutorial/tutorial.html#why-immutability-is-important)
* It is a good software engineering practice to avoid altering application data directly
* Instead, when application data changes it is generally best to replace data with a new copy reflecting the desired changes 
* By keeping data immutable (that is, not changing it directly) complex features become easier to implement 
* It is also easier to keep track of changes in immutable data than it is for mutable data. This is because mutable data are modified directly, but immutable data can easily be compared to previous versions of itself
* Keeping data immutable also helps to keep React components pure without any side effects, and this is one of the key advantages of React for building complex scalable client-side applications
* In light of this, when altering application state it is best to use methods that return a new array or object rather than altering data in place. Array methods like `map` and `filter` return new arrays, for instance 

### `this.setState` 
* The `state` property is never directly accessed in React
* You use the `this.setState` method
* [setState](https://reactjs.org/docs/react-component.html#setstate)
* `setState` takes an updater object as its first argument that defines the new data for a `state` property 
* E.g: 
```js
this.setState({
    name: "New Name",
    age: 30
});
```
* `setState` can also take a callback to execute after state has been updated. If you need to run any code that depends on the updated state, it is best to pass it as a callback since the updating of state will sometimes happen asynchronously
* If you want to update `state` based on previous `state` data, you can pass an anonymous function to `setState` that takes the previous application state as an argument: 
```js 
this.setState(prevState => ({
    name: `${prevState.name} ${newName}`
}));
```
* (note: the above examples are not real-world examples by any means. They just exemplify the syntax for reference)

## Going further 
* These notes are very basic and do not give real-world examples 
* Here are a few links to some great React tutorials: 
    * [Getting Started with React](https://www.taniarascia.com/getting-started-with-react) by [Tania Rascia](https://www.taniarascia.com/). She's got a lot of great content!
    * [Tutorial: Intro to React](https://reactjs.org/tutorial/tutorial.html) by the React team
* It is also good to jump into the [documentation](https://reactjs.org/docs/getting-started.html) since React has really great documentation

