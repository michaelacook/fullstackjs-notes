# JavaScript and The DOM
* JavaScript got its start in 1995 as a simple language used to make web pages respond to user actions
like mouse clicks, etc
* Fundamentally JavaScript is meant for manipulating HTML
* Manipulating the DOM involves selecting HTML elements, altering them, and listening for, then responding
to user interaction with the page

### JavaScript global environment
* The global environment: the environment, or set or variables, objects and methods within which JavaScript operates
* When JavaScript operates in the browser, the browser environment provides a number of built-in objects and methods that allow you to manipulate web pages
* The browser environment can be explored in the developer tools window in your web browser
* Global variables and methods, like `location` or `alert()` for instance, are properties and methods of the global environment object `Window`
* E.g: `window.location.href`, `window.alert('hello world')`
* All the properties and methods of the global `window` object can be accessed and viewed by typing 'window' into the browser JavaScript console
* Web page manipulation is done through the global object `Document` which is an internal representation that maps to the actual HTML page

### The Document Object Model (DOM)
* The `Document` objects is itself a property of the global `Window` object
* Internal representation of the web page HTML that JavaScript interacts with
* It is a model, not the actual HTML, but it maps to the actual HTML so that changes to the DOM map to changes to the HTML page
* Represents a web page as a family tree like structure
* HTML elements in the web page are represented as nodes in the DOM, and their relationship to one another is spoken about in family-like terms, with parent, child, grandchild, and sibling nodes etc

### Selecting elements
* `document.getElementById(id)` allows you to select an element by its unique id name
* `document.getElementsByTagName(tagName)` allows you to select all elements of a certain type, returning an iterable `collection` of elements
```js
const paragraphs = document.getElementsByTagName('p');
```
* `document.getElementsByClassName(className)` allows you to select all elements of a class and returns an iterable collection similar to `getElementsByTagName`
* (note: you can select an element starting from a node further down the DOM tree than the document, in order to narrow the query. You do not always need to get a reference to a node starting at `document`)

#### Query selectors
* `document.querySelector(selector)` and `document.querySelectorAll(querySelector)` both accept ids, class names, tag names, html attribute, etc.
* `querySelectorAll` returns an iterable collection
* `querySelector` returns the first matched element
* The selected argument passed to the method must be a valid html id, class, attribute etc. E.g. to select an element with the id of 'main' you would use `document.querySelector('#main');`
* To select elements based on attribute, use the following syntax:
    `document.querySelector('[attribute=name]');`
* E.g:
```js
document.querySelector('[title=label]');
<p title="Label">This is some text</p>
```
* Query selectors can also accept CSS pseudo-selectors as arguments
* You can select all elements of a certain type and a certain class name with
`document.querySelector('element.classname')`:
```js
document.querySelector('p.example-class');
```

### Altering the DOM
* Methods and properties for altering text and content in the DOM.

#### Changing text
* `Element.textContent` can be used to either read, or set the text of an element
* To set text content, assign a string to the property with the `=` operator
* To read text content, use the `textContent` property
* `Element.innerHTML` functions similarly to `Element.textContent` but can also read and set html inside an element

#### Changing attributes  
* `Element.attribute` can be used to read or set an element's html attributes
* If you need to read or set class, use `Element.className` instead
* E.g:
```js
const img = document.querySelector('.example-img');
img.title = 'Example title';
```
* To read or set the value of an input, use `Element.value`

#### Changing styles
* Each element object in the DOM has a `style` property that is itself an object
* Styles can be read and set using `Element.style.property`
* The `styles` object on the element represents only inline styles, and does not contain any data about styles applied via an external stylesheet
* Thus `Element.style` is most often used as a setter rather than a getter

#### Creating and removing DOM elements
* `document.createElement(elementType)` is used to create an element to the DOM
* E.g: `document.createElement('p');`
* You can then set the text content or inner html on the created element before appending it to the DOM
* To add the element to the DOM, use `node.appendChild(element)` to append the element to the parent you select
* To remove an element use `node.removeChild(childElement)`

### Responding to user interaction
* Browsers listen for user events (clicks, mouse moves, keydowns, etc) and you can write functions that will run when an event takes place, as well as gain information about the user interaction from the `event` object and use that information to perform the right action
* [Read about DOM events](https://developer.mozilla.org/en-US/docs/Web/Events)
* To respond to an event, you add an event listener to the element you want to listen for events on, and pass a callback to the event listener to perform the desired action, and pass the `event` object to the callback as an argument
* E.g:
```js
document.addEventListener('click', e => console.log(e.target));
```
* The example above will log out the element clicked on by accessing the event `target` property

#### Event bubbling and delegation
* Events begin at the element listening for them, but then "bubble up" through the DOM, touching all ancestors as they go
* Thus clicking on a target element causes the same click event to trigger any other click event listeners on any ancestor elements as well
* Because of event bubbling, you can set an event listener on a parent element and it will receive the event object from any event triggered on any of the children. This allows you to write fewer event handlers
* E.g consider a list, and you want each item to capitalize when clicked. An event listener does not need to be added to each list item. Instead:
```js
document.write(`
    <h1>My List:</h1>
    <ul id="myList">
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
    </ul>
`);
document.getElementById('myList').addEventListener('click', e => {
    e.target.textContent = e.target.textContent.toUpperCase();
});
```
* In the example, the event listener is listening on the ul. When the event bubbles to the ul from the individual lis, the event listener's callback accesses the event object's `target` property which represents the element the event was first triggered on. It then calls the `toUpperCase` string method on the text content of the event target
* In other words, the action specified in the event listener callback is ***delegated*** to the target element
* It is also possible to access more information about the target element, such as tag name, other attributes, etc. and only run the callback on the desired type of element, or on elements with a certain classname, attribute, etc.


### DOM traversal
* How to get a reference node from a node that is close to it, rather than starting from the top of the DOM tree (document object)
* `Element.parentNode` is used to obtain a reference to an element's parent node. This can be useful when you need to remove an element, but to remove an element you need a reference to it's parent node using `parentNode.removeChild(el)`
* Get a reference to the previous element sibling with `element.previousElementSibling`
* This is preferred over `element.previousSibling` which will not always get a reference to an actual DOM node but sometimes an empty string
* Get a reference to the next element sibling using `element.nextElementSibling`
* Insert a node into the DOM before a node with `parentNode.insertBefore(newNode, referenceNode)`
* Get all child element nodes of a node with `parentNode.children`. This will get an iterable collection
* Get the first element child of a node with `parentNode.firstElementChild`
* Get the last element child of a node with `parentNode.lastElementChild`
