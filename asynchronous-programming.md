# Asynchronous JavaScript
* JavaScript is a synchronous, single-threaded language: it can only do one thing at a time
* Asynchronous programming is the process of writing code that avoids blocking behaviour caused by resource-intensive operations that take time
    * Callback functions are asynchronous, because they are only called at a later time when the program needs them rather than right away
* Asynchronous programming is very useful for allowing other parts of a program to continue operating while other more resource-intensive or slow parts of a program run in the background (i.e http requests)
* JavaScript provides tools to handle asynchronous operations
* The basis of asynchronous programming is being able to get other stuff done while stuff that takes longer to do completes
    * Without asynchronous code, interactivity in the browser would be difficult or impossible because each function in the script would have to run before the next could be executed, causing the page to freeze
    * E.g. event listeners allow the programmer to specify a callback to be called at a later time when the correct user input occurs, rather than being called immediately and thus preventing the flow of control through the script

#### Synchronous and asynchronous examples
* Running a `while` loop under normal circumstances will block all other interactivity in a web browser while the loop runs. This is an example of blocking behaviour. For instance, running a while loop in the console on a web page will prevent you from being able to interact with any forms or buttons or functions of the page until the loop completes
* E.g:

```js
const btn = document.getElementsById('btn');

function wait() {
    console.log('starting...');
    let start = new Date().getTime();
    while (new Date().getTime() - start < 8000);
    console.log('finished!');
}

btn.addEventListener('click', wait);
```
* The code above will block all other functions of the page while it completes
* But there is a way to do the same thing without introducing blocking behaviour into the script. In other words, you can do the same thing in a way that will not prevent other parts of the program from executing
* E.g:

```js
const btn = document.getElementsById('btn');

function carryOn() {
    console.log('starting...');
    setTimeout(() => {
        console.log('finished');
    }, 8000);
}

btn.addEventListener('click', carryOn);
```
* The code above will produce the exact same output in the browser console, but without blocking any functions or interactivity on the page. This is because `setTimeout` is a way to run code asynchronously
* `setTimeout` accepts a **callback** which is a function that will be executed at a later time when it is needed, rather than immediately. Thus control flow can continue to flow through the program, and jump back up to execute the callback at the appropriate time
* A more realistic example is sending ajax requests. Once the request is sent the rest of the script can still operate without any blocking behaviour, and when the response is received the callback supplied for the ajax request is executed asynchronously
* Tasks that take time should be run asynchronously so that the user can continue to interact with the app!

### The call stack
* [MDN docs](https://developer.mozilla.org/en-US/docs/Glossary/Call_stack)
* [Treehouse Call Stack reading material](https://teamtreehouse.com/library/asynchronous-programming-with-javascript/what-is-asynchronous-programming/the-javascript-call-stack)
* Every JavaScript engine has a mechanism called the **call stack** which keeps track of the order of function calls and where the interpreter currently is in the program at any given point
* The call stack manages each function that is being called as well as functions called from within it
* The call stack can process only one function at a time (single-threaded)
* When a function is called, it gets pushed onto the top of the call stack. If any functions are called within that function, they then get pushed to the top of the call stack. When a function is done executing, it gets popped off the top of the call stack and then the following function call gets pushed up to the top for processing
* After the last function call is executed, the call stack is empty and the program finishes
    * Unless the user makes a demand on the program, initiating a function call. Then that function call is pushed onto the call stack
* If there are more function calls in the call stack than it can handle with its memory allocation, it results in a "stack overflow"
* Consider the following example:

```js
function func3() {
  console.log('Students!');
}

function func2() {
  console.log('Treehouse');
}

function func1() {
  console.log('Hi');
  func2();
  func3();
}

func1();  

// Hi
// Treehouse
// Students!
```
1. func1() is invoked, which logs Hi to the console
2. func2() is pushed onto the stack, executes and logs Treehouse to the console
3. func2() is done executing, so it's popped off the stack
4. func3() is pushed onto the stack, invoked and logs Students! to the console
5. func3() is done executing, so it's popped off the stack
6. At this point func1() is done executing all of its functions, so it's popped off the stack
7. The call stack is empty

#### Web APIs
* The call stack handles asynchronous code in a different way than it handles typical synchronous tasks
* When the JavaScript engine encounters asynchronous code that has a callback to be executed at a different point, it hands that code off to a web API (DOM, XMLHttpRequest, Timeout, Fetch). Then callstack continues on as normal, and the callback gets inserted back into the call stack at a later point
* It is not the JavaScript engine itself but the runtime environment (web browser, Node.js) that provide the APIs that allow JavaScript to perform asynchronous tasks
* Asynchronous tasks can be handled by a number of web APIs such as `setTimeout`, `XMLHttpRequest`, the Fetch API and the `document` object
* Consider the following example of an asynchronous call stack:

```js
function func3() {
  console.log('Students!');
}

function func2() {
  console.log('Treehouse');
}

function func1() {
  console.log('Hi');
  setTimeout(func2, 1000)
  func3();
}

func1();

// Hi
// Students!
// Treehouse
```
* Even though `func1` calls `func2` before `func3`, because it passed it as a callback to `setTimeout`, the call stack moves on and executes `func3` first. Once `func2` is ready to execute, it gets put into the call stack at a later time
* When a callback is handed off to a web API, it gets pushed into the **callback queue** where it waits to be placed into the call stack
* The fact that async tasks get taken out of the callstack prevents blocking behaviour

### The callback queue and event loop
* [Callback queue and event loop Treehouse reading](https://teamtreehouse.com/library/asynchronous-programming-with-javascript/what-is-asynchronous-programming/the-callback-queue-and-event-loop)
* [MDN Concurrency model and Event Loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Queue)
* When a web API is done processing an async task, it hands the callback off to something called the **callback queue** which is a [queue](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Queue) or lineup or all the async task callback functions waiting to be executed
* The callback queue is just like a holding area for all the asynchronous callbacks waiting to be put back into the callstack at their determined times
* Only callbacks for non-blocking operations scheduled to run asynchronously are added to the callback queue (e.g `setTimeout` and `setInterval`, ajax requests)
* Consider a `setTimeout` function that executes a callback after 1000ms. After 1000ms the timeout API passes the callback to the callback queue, where it sits waiting to be added into the call stack. This is why a callback may actually not execute for slightly longer than the specified timeout, since it is not immediately added back into the call stack but has to wait to be inserted by the event loop

#### The event loop
* The **event loop** monitors the call stack and the callback queue, and every time the call stack becomes empty, the event loop puts the first task in the callback queue into the call stack for execution

## Asynchronous programming using callbacks
* A higher-order function: takes n > 0 functions as args and/or returns n > 0 functions
* Asynchronous programming is basically writing instructions for the program to use in response to an event, data request or specified interval of time. Since asynchronous programming is basically writing code to operate in the future, callback functions are one way to do it
* This can lead to "callback hell" or "pyramid of doom" which can make a program difficult to read and follow
* Ane example of programming asynchronously with callbacks:

```js
const astrosUrl = 'http://api.open-notify.org/astros.json';
const wikiUrl = 'https://en.wikipedia.org/api/rest_v1/page/summary/';
const peopleList = document.getElementById('people');
const btn = document.querySelector('button');

// Make an AJAX request
function getJSON(url, callback) {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.onload = () => {
    if(xhr.status === 200) {
      let data = JSON.parse(xhr.responseText);
      /* not totally necessary to use return keyword,
      just ensures first function fully completes execution */
      return callback(data);
    }
  };
  xhr.send();
}

// Iterate over data and get wiki data for each
// For each item in wiki data, generate html
function getProfiles(json) {
  json.people.forEach(person => {
    getJSON(wikiUrl + person.name, generateHTML);
  });
}

// Generate the markup for each profile
function generateHTML(data) {
  let section = document.createElement('section');
  peopleList.appendChild(section);
  section.innerHTML = `
    <img src=${data.thumbnail.source}>
    <h2>${data.title}</h2>
    <p>${data.description}</p>
    <p>${data.extract}</p>
  `;
}

// Event listener
btn.addEventListener('click', () => {
  btn.style.display = 'none';
  getJSON(astrosUrl, getProfiles);
});
```
* In the example above, the `getJSON` function accepts a url and a callback as arguments. It gets data from the url, and then passes that data as an argument to its callback when it returns
* This avoids all blocking behaviour by handing off the function that handles the data to a web API, which then hands it off to the callback queue once it's parent function returns
#### Pitfalls of callbacks for async
* Though the code above works well, it's slightly tricky to follow because of the nested callback functions
* A much bigger async program written in the same way could be very difficult for other developers to read and understand because of many nested callbacks
* It is also possible to end up in "callback hell" or the "pyramid of doom" where you have many nested callbacks all in one function, resulting in code that is very difficult to read and understand
* Async tasks could be handled well by callbacks in a very small program, but JavaScript has other built-in features such as **promises** that allow you to program asynchronously in a way that is more straight-forward for larger applications

## Promises
* [Treehouse promise reading material](https://teamtreehouse.com/library/asynchronous-programming-with-javascript/understanding-promises/promises-review)
* Promises offer a more elegant and readable way to program asynchronously, as well as offer more control
* A promise represents the eventual completion of an asynchronous operation

##### Callbacks vs. promises
* Callbacks are only executed once something else is executed, and if something goes wrong there is no guarantee that the callback will ever be executed
* A promise is just that - a promise that some piece of code is going to be executed at some future point, or, some value on failure
* Promises are always in one of three states:
    1. Pending - default state. Waiting
    2. Fulfilled - operation completed successfully
    3. Rejected - operation failed

#### Using promises
* Using promises is a three step process:
    1. Instantiate a promise: `const promise = new Promise();`
        * Constructor accepts a single argument which is a callback with two parameters: `resolve` for when promises is fulfilled, and `reject` for when it's rejected
    2. Define what should happen when the promise is either fulfilled or rejected
    3. Consume the value of a fulfilled promise or provide a rejection reason for a rejected promise
* A simple promise example:

```js
const order = false;

const breakfastPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
     if (order) {
        resolve('Your order is ready!');
     } else {
        reject(Error("Your order could not be fulfilled."));
     }          
  }, 6000)
});

breakfastPromise
  .then(val => console.log(val))
  .catch(err => console.log(err));
```
* once a promise is instantiated, you can use the `.then()` and `.catch()` methods to determine what happens on success or fail of the operation
* The value returned by `resolve` and `reject` are immediately available inside `.then()` and `.catch()`
* `.then()` always returns a new promise, so they can be chained together to perform a series of operations on promise resolve
* A more realistic example:

```js
const astrosUrl = 'http://api.open-notify.org/astros.json';
const wikiUrl = 'https://en.wikipedia.org/api/rest_v1/page/summary/';
const peopleList = document.getElementById('people');
const btn = document.querySelector('button');

function getJSON(url) {
  return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.open('GET', url);
      xhr.onload = () => {
        if(xhr.status === 200) {
          let data = JSON.parse(xhr.responseText);
          resolve(data);
        } else {
          reject(Error(xhr.statusText));
        }
      };
      xhr.onerror = () => reject(Error('A network error occurred.'));
      xhr.send();
  });
}

function getProfiles(json) {
  const profiles = json.people.map( person => {
    return getJSON(wikiUrl + person.name);      
  });
  /* instead of returning an array of promises,
  return an array of all the data from all the promises.
  Promise.all() accepst an array of promises and returns the data
  from all of them as just one single promise basically.
  Promise.all() will fail as soon as one promise in the array
  fails */
  return Promise.all(profiles);
}

function generateHTML(data) {
  data.forEach(person => {
      const section = document.createElement('section');
      peopleList.appendChild(section);
      section.innerHTML = `
        <img src=${person.thumbnail.source}>
        <h2>${person.title}</h2>
        <p>${person.description}</p>
        <p>${person.extract}</p>
      `;
  });
}

btn.addEventListener('click', (event) => {
  event.target.textContent = "Loading...";
  getJSON(astrosUrl)
    .then(getProfiles)
    .then(generateHTML)
    .catch(err => {
      peopleList.innerHTML = '<h3>Something went wrong. Sorry!</h3>';
    })
    .finally(() => event.target.remove());
});
```
* `Promise.all()` takes an array of promises and returns a single promise, but fails if any of its subpromises are rejected
`Promise.finally()` accepts a callback you wish to execute when a promise chain fully completes

##### `Fetch` API
* `fetch` is a cleaned-up ES6 way to do ajax requests and is promise-based
* E.g:

```js
fetch(url).then(response => response.json());
```
* The global `fetch` function accepts a url and returns a promise that resolves a response. That response is accessible in `then` and by calling `.json` on the response object you can get the JSON data. You can then chain `then` clauses to perform the series of operations you want to do with that data:

```js
fetch(url)
    .then(response => response.json())
    .then(updateDOM)
    .then(removeButton)
    .catch(showError)
    .finally(finishSequence);
```
* (note: each function called in then `then` chain must itself return a promise in order for the next function in the sequence to do anything)

### Summary
* To program asynchronously with promises,
    1. Define a function for your initial asynchronous operation and have it instantiate and return a promise object
    2. Inside the returned promise, define your async operation. Call the `resolve` and `reject` methods wherever appropriate, passing them the data you want available in `then()` and `catch`
    3. Write any other functions you want to run after the initial async task and chain them to the promise using `then()` (remember any data, for instance from an ajax request, that you want to pass to these functions will be available in `then()` if you pass it to `resolve()`)
    4. Write a `finally()` to complete the chain. This could be as simple as updating the DOM, displaying a message, whatever you need. (optional)

## Async/Await
* async/await provides a simplified syntax for working with promises
* async functions always return a promise; it is syntactic sugar for promises
* Syntax:

```js
async function fetchData(url) {
    const response = await fetch(url); // waits on a resolved promise from fetch()
    const data = await response.json(); // also async and returns a promise, so needs await
    return data;
}
```
* The `await` keyword pauses the execution of an async function and waits for the resolution of a promise
* It then resumes execution and returns the resolved value
* Pausing execution does not introduce blocking behaviour: it is asynchronous
* can only use the `await` keyword inside functions prefixed with the `async` keyword
* Async functions **always return a promise**; that promise resolves with the value returned by the async function, or rejects with an error thrown from within the function
* Therefore you can chain a `then` to the async function call to do something with it's return value (usually data from a server):

```js
async function fetchData(url) {
    const response = await fetch(url);
    const data = await response.json();
    return data;
}

fetchData(url)
    .then(updateDOM)
    .finally(finishSequence);
```

* Example of the previous example program written with async/await:

```js
const astrosUrl = 'http://api.open-notify.org/astros.json';
const wikiUrl = 'https://en.wikipedia.org/api/rest_v1/page/summary/';
const peopleList = document.getElementById('people');
const btn = document.querySelector('button');

// Handle all fetch requests
async function getPeopleInSpace(url) {
  const peopleResponse = await fetch(url);
  const peopleJSON = await peopleResponse.json();

  const profiles = peopleJSON.people.map(async person => {
    const craft = person.craft;                              
    const profileResponse = await fetch(wikiUrl + person.name);
    const profileJSON = await profileResponse.json();

    return { ...profileJSON, craft };
  });

  return Promise.all(profiles);
}

// Generate the markup for each profile
function generateHTML(data) {
  data.map( person => {
    const section = document.createElement('section');
    peopleList.appendChild(section);
    section.innerHTML = `
      <img src=${person.thumbnail.source}>
      <span>${person.craft}</span>
      <h2>${person.title}</h2>
      <p>${person.description}</p>
      <p>${person.extract}</p>
    `;
  });
}

btn.addEventListener('click', (event) => {
  event.target.textContent = "Loading...";
  getPeopleInSpace(astrosUrl)
    .then(generateHTML)
    .catch(err => console.log(err))
    .finally(() => event.target.remove());
});
```

#### Error handing with `try...catch`
* Using a `try...catch` block inside an async function is the cleanest way to catch errors
* Because async/await is promise-based, you can still use `catch()` but using a `try...catch` block is more elegant and avoids nesting
* E.g:

```js
/* sends an http request and awaits its response,
returns a promise containing response JSON */
async function getJSON(url) {
  try {
    const response = await fetch(url);
    return await response.json();
  } catch (error) {
    throw error;
  }
}

async function getPeopleInSpace(url) {
  const peopleJSON = await getJSON(url);

  // iterate over all astronauts and make request for wiki data
  const profiles = peopleJSON.people.map(async person => {
    const craft = person.craft;                              
    const profileJSON = await getJSON(wikiUrl + person.name);

    return { ...profileJSON, craft };
  });

  // return single promise for all astronauts wiki data
  return Promise.all(profiles);
}
```
* The above code shows a refactored version of previous examples that handles errors
* This avoids nested `catch()` chains and catches any potential errors as early on in the chain of events as possible 
