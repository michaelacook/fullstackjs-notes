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
* (note: some parts of these notes assume basic knowledge of the DOM and built-in JavaScript DOM manipulation methods)

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


## `setTimeout()` and `setInterval()`
* Callbacks are handy for timers, since timers are meant to perform a certain action once, or repeatedly, after a given or variable amount of time
* E.g:

```js
const surpriseSection = document.getElementById('surprise');

const randomTime = Math.random() * 6000;

setTimeout(() => surpriseSection.textContent = '🎉 Surprise! 🎉', randomTime);
```

* A more complex example using the `setInterval()` timer:

```js
// A simple 24 hour JavaScript clock

const clockSection = document.getElementById("clock");

const getTime = () => {
    const pad = number => {
        if (number < 10) {
            return "0" + number;
        } else {
            return number;
        }
    }

    const now = new Date();

    const hh = pad(now.getHours());
    const mm = pad(now.getMinutes());
    const ss = pad(now.getSeconds());

    return `${hh}:${mm}:${ss}`;
}

setInterval(() => clockSection.textContent = getTime(), 1000);
```

* note: the instructor for the course doesn't think an anonymous function is appropriate for the clock application because the clock will only start one second after page load. I disgree and find nothing undesirable about this, but the next example will show the method the instructor uses:

```js
const runClock = () => clockSection.textContent = getTime();

tickTock();
setInterval(runClock, 1000);
```

* In the example above, the callback is defined and not passed as an anonymous function, allowing you to call the function once at page load, and then repeatedly on an interval after. Some might find this more desirable, but it depends on your needs or point of view

## Callbacks with arguments
* Callback functions can specify any number of parameters
* Syntax:

```js
const foo = (arg1) => // do something

const execute = (arg2, callback) => callback(arg2);

execute(arg, foo);
```

* A more complex example:

```js
// variables pointing to DOM elements
const div1 = document.getElementById('first');
const div2 = document.getElementById('second');
const div3 = document.getElementById('third');

// functions applying styles to a DOM element passed as an argument
const makeRed = element => element.style.backgroundColor = "red";

const makeBlue = element => element.style.backgroundColor = "blue";

// function executing a callback with an argument
const addStyleToElement = (element, callback) => callback(element);

// execution of the callback
addStyleToElement(div1, makeRed);
```

### Callbacks and the DOM
* Callbacks are frequently used in programming DOM interactivity
* The `element.addEventListener(eventType, callback)` method accepts an event object and a callback function
* A more complex example:

```js
const nameInput = document.getElementById('name');
const messageTextArea = document.getElementById('message');

nameInput.addEventListener('click', e => {
    e.target.className = 'highlight';
});

messageTextArea.addEventListener('click', e => {
    e.target.className = 'highlight';
});
```
* You may also see the old syntax in legacy code:

```js
nameInput.addEventListener('click', function(e) {
    e.target.className = 'highlight';
});
```
* Here `e` is the variable pointing to the `event` object

* A more DRY example:

```js
const nameInput = document.getElementById('name');
const messageTextArea = document.getElementById('message');

const focusHandler = e => e.target.className = 'highlight';
const blurHandler = e => e.target.className = '';

nameInput.addEventListener('focus', focusHandler);
nameInput.addEventListener('blur', blurHandler);
```
* The above example avoids repetition of the first example and represents a better approach
