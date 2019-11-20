# Callback Functions in JavaScript
* Callbacks are functions that are not called explicitly by the programmer, but by the computer at a future time
* Synchronous programming: function executes immediately when it is called
* Asynchronous programming: function executes at a later point in time when it is required
* Asynchronous programming allows the computer to perform other tasks while waiting for an event to occur
* Common uses for callbacks:
    * one-off and repeat timers
    * user interaction
    * loading data from a web server with AJAX
    * opening files in node.js
    * Lots of other scenarios - callbacks are common!
* Asynchronous programming allows for flexibility and speed in building user interafaces

## What is a callback?
* A function passed to another function as a parameter
* A regular function that can have any functionality
* `executeCallBack(callBack);`
* Notice no parentheses when the name of the callback is passed to another function. This is because the developer is not calling the function but instead giving the name of that function so that the function receiving the callback as an argument can execute the callback when needed
* Functions are first-class objects in JavaScript: they can be used the same as other variables
* A simple example:

```js
const sayHello = () => console.log('Hello');

const executeCallback = callback => callback();

executeCallback(sayHello);
```

## Anonymous functions
* A function without a name
* Passing anonymous functions as callbacks is commonly done and is a more explicit way of writing callbacks
* Usually an anonymous function is supplied as an expression passed to another function as a callback
* E.g:

```js
executeFunction(function() {
    console.log('Hello');
});
```

### ES6 syntax
* ES2015 allows for anonymous functions passed as callbacks to use the shortened arrow function syntax, making them more concise
* E.g

```js
executeCallback(() => console.log('Goodbye'));
```

* The same rules for constructing arrow functions discussed in [ES2015](ES2015.md) apply here
