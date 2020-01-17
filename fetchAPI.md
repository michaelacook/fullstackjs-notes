# The Fetch API
* The fetch API is a data fetching interface native to the browser that is simpler way than using
the `XMLHttpRequest` object
* Avoids the "pyramid of doom" that can occur with a lot of nested callbacks common to ajax programming
* The fetch API is promise-based
* [Using Fetch docs](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)


## Syntax

```js
fetch(url);
```
* At it's most basic, the above example is how to structure a request using the fetch api
* By default, `fetch` sends a `GET` request, but can be customized to send `POST` requests
* (note: by default, `fetch` won't send or receive cookies from the server, and you may need it to. Your fetch can be customized, consult docs)
* Because `fetch` is promise-based, it returns a `promise` and you can use all promise methods on it:

```js
fetch('https://dog.ceo/api/breeds/image/random')
  .then(response => response.json())
  .then(data => console.log(data.message));
```
* Write a reusable fetch function:

```js
function fetchData(url) {
  return fetch(url)
    .then(response => response.json());
}
```

## Error handling
* Write a function to check if `fetch` retrieved the data we want:

```js
function checkStatus(response) {
  if (response.ok) {
    return Promise.resolve(response);
  } else {
    return Promise.reject(new Error(response.statusText));
  }
}
```
* This function can then be called inside the `fetchData` function:

```js
function fetchData(url) {
    return fetch(url)
        .then(checkStatus)
        .then(response => response.json());
}
```
* This function will return a promise that rejects if the response status code is not `200 OK`. If the response code is 200 then the returned promise will return a resolved promise containing the response JSON
* The same could be achieved using the `catch()` method but the above example is more modular

## `Promise.all()`
* The `all()` method accepts a string or more commonly array of individual promises and returns a single promise containing an array of the fetched data in the order in which the data was fetched
* This is a way to consolidate multiple fetch requests into a single promise that is useful when you need to wait for multiple fetches to resolve and be used together
* E.g:

```js
Promise.all([
  fetchData('https://dog.ceo/api/breeds/list/all'),
  fetchData('https://dog.ceo/api/breeds/image/random')
]).then(data => {
  const breedList = data[0].message;
  const randomImage = data[1].message;

  generateOptions(breedList);
  generateImage(randomImage);
});
```
* The above code passes two fetches to `Promise.all` and then calls the `then()` method on the resolved promise and gets the data from each fetch and calls the appropriate method for that data
* It is important to note that `Promise.all()` will reject if even a single promise passed to it rejects, even if all others resolve

## Sending `POST` requests
* To post data to the server with `fetch` you must include a second argument containing the configuration for the post
* The second argument is an object that contains the request method, request headers, and the body or payload of the post request in JSON format
* E.g:

```js
function postData(e) {
  e.preventDefault();
  const name = document.getElementById('name').value;
  const comment = document.getElementById('comment').value;

  // this is required for POSTing data
  const config = {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      // in ES6 this could be shortened to { name, comment }
      body: JSON.stringify({ name: name, comment: comment })
  }  

  fetch('https://jsonplaceholder.typicode.com/comments', config)
    .then(checkStatus)
    .then(response => response.json())
    .then(data => console.log(data));
}
```
* The above function prevents default form submitting behaviour, then gets the form field values. It then creates a configuration object that sets the http method, headers, and formats the payload data as JSON using the `JSON.stringify()` method. The configuration object is then passed to the fetch

#### `JSON.stringify`
* [Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
* The `JSON.stringify()` method accepts a JavaScript object or array as an argument and converts it to JSON format
* It is necessary to convert JavaScript data to JSON format before sending it to a server or another application
