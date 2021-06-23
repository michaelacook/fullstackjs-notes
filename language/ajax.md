# JavaScript AJAX
* AJAX, hereafter 'ajax', stands for **Asynchronous JavaScript and XML**
* ajax allows you to update html without reloading the web page
* Using ajax, you can both send, and receive data from the web server ***asynchronously***, meaning outside the normal request-response cycle
* ajax requests and responses are useful because it allows you to update small parts of a webpage without reloading, improving speed and user experience
* **XHR** refers to the `XmlHttpRequest` object which is an ajax request. These can be viewed in the JavaScript console under the network tab in Chrome
* First introduces by Microsoft in 1999 with Internet Explorer 5

## How AJAX works
* Technically ajax is just called the `XMLHttpRequest` object or XHR
* [Documentation for XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
* The [Web Sockets API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) is a way other than AJAX to open a two-way communication session between a client and server
* The real name of the object refers to XML is because originally, XML was seen as the format in which data should be sent from the server, but this is no longer the case. Both XML and JSON are acceptable and JSON has become more popular for its simplicity. The XHR object can use both
* The AJAX programming process is as follows:
    1. Create an XHR object
    2. Definte a callback to be run at receipt of an HTTP response that processes response data and updates the page
    3. Open a request - accepts HTTP request method (GET or POST), and URL
    4. Send the request

## A simple example
* Because ajax is asynchronous, you never know which request (if more than one) will be handled by the server first, because the server may take more or less time for different requests. ajax handles responses as they come in, not in the order of their requests. So you never know what order your callbacks will be executed in. To trigger the callback, the ajax object listens for change in the `readyState`
* Each ajax request needs its own XHR object
* A readyState of 4 means the server has responded
* Example:

```js
// Instantiate the XHR object
const xhr = new XMLHttpRequest();

// Open a request
xhr.open('GET', 'example.html');

// Define a callback to run on receipt of response
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4) {
    document.getElementById('ajax').innerHTML = xhr.responseText;
  }
};

// Send request
xhr.send();
```
* The `send()` method can be run on a regular browser event like a click, form submission etc

#### GET and POST
* Two most common http request methods are GET and POST
* GET is for getting data from the server
    * When using the GET method with ajax, we just need to specify GET and a URL, which could contain query strings to run specific server-side scripts
    * (note: `&`, `space` and `+` cannot be used in query strings without changing their behaviour - they need to be urlencoded):
    | & | Space | + |
    |---|-------|---|
    | % |   +   | %2B|
    * There are utilities online to urlencode strings, and JavaScript has methods to do it as well
* POST is used for sending data to the server that will be processed or saved
    * GET requests display data in the query string, thus not secure for sensitive data
    * POST sends data in the body of the HTTP request, thus not as visible
    * This also lets you send a lot more information to the server
    * POST requests require a header specifying the type of data the server is going to receive


#### Response formats
* Some times you just want to request a simple static file or some HTML from the server
* Other times you are getting a lot of information, and this is where JSON comes in handy because it provides a structure and pointers to specific data in the response
* JSON stands for JavaScript Object Notation and is a universal way to format data that looks like a JavaScript object but can be parsed more easily and shared between many different programming languages
* There is also XML, which is not as common because though it is simple in structure, it is difficult to parse in JavaScript

## AJAX security limitations
* ajax requests are limited by a browser's [same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy) meaning the request has to come from the same server that the response is coming from, but cannot make requests of a different web server
* Thus for instance, you can't use AJAX to try and communicate with someone else's bank account online
* This is for security reasons
* ajax requests won't work if your site uses HTTP but the URL you are sending the request to uses HTTPS
* Also cannot switch ports or hosts
* There are legitimate ways to circumvent the same-origin policy:
    * Create a web proxy
    * Use JSONP - JSON with padding. This is how CDNs work  
    * [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) - cross-origin resource sharing
* Also note: ajax won't work unless you are getting your html files through a web server. Therefore, when developing a web application that uses ajax, you will need to set up a local web server and get it to serve you your files

## AJAX callbacks
* Each stage of the ajax process involves the ready state - the ready state of the request changes at each stage of the programming process
* The `readyState` property of the XHR object is a number between 0 and 4 representing each state of the ajax request: 0 represents instantiation, 3 represents response coming back, 4 represents response received
* Sometimes something will go wrong and the server will not return a response for any number of reasons. You can add an additional condition to an ajax callback checking for a server status of 200 meaning "Ok":
```js
xhr.onreadystatechange = () => {
    if (xhr.readyState === 4 && xhr.status === 200) {
        // update DOM, etc
    }
}
```
* This will run the logic only if the server status is Ok and the ready state is 4
* You could also add logic to your callback to deal with a 404 or a 401 server status (not found, not authorized) and other server statuses as well
* You can also access the `statusText` property which contains the text for the current server status at receipt of response. For instance, 404 would be "Not Found" or some variant, and 200 would be "OK"
* These messages could be used to print out an error message to the user
* E.g:
```js
if (xhr.status === 404) {
    alert(xhr.statusText);
}
```
* [Docs for the `readyState` property](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/readyState)

## JSON
* Uses JavaScript objects and arrays to exchange data
* JSON can use either array or object notation and it is common to combine both
* JSON file extension is .json
* One difference between JSON objects and regular JavaScript objects is that keys must be double-quoted
* There are tools online to validate your JSON, since JSON must be correctly formatted to work. [https://jsonlint.com/](https://jsonlint.com/) is one tool you can use

#### Parsing JSON with JavaScript
* Though JSON looks like JavaScript, it isn't - it is just plain text to a browser
* To be used, JSON must be converted from a simple string to usable JavaScript
* This is called **parsing**
* To obtain a valid JavaScript object from JSON text, pass the `responseText` of the ajax request to the `JSON.parse()` method
* `JSON.parse()` returns a valid JavaScript object (including arrays) when valid JSON is passed to it
* E.g:

```js
const request = new XMLHttpRequest();

request.onreadystatechange = () => {
  if (request.readyState === 4) {
      // parse data
      const employees = JSON.parse(request.responseText);
  }
};

request.open('GET', 'data/employees.json');
request.send();
```
* You can then iterate over the data and do whatever you need to do with it and update the page
* Example of a callback updating the page:

```js
request.onreadystatechange = () => {
  if (request.readyState === 4) {
      const employees = JSON.parse(request.responseText);
      const employeeList = document.getElementById('employeeList');
      let ul = '<ul class="bulleted">';
      employees.forEach(employee => {
        ul += `<li class=${employee.inoffice ? "in" : "out"}>${employee.name}</li>`;
      });
      ul += '</ul>';
      employeeList.innerHTML = ul;
  }
};
```
