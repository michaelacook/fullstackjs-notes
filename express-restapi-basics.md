# REST APIs in Express
* REST is an acronym that stands for Representational State Transfer 
* REST APIs are server-side programs that perform the business logic for an application but do not include a user interface. As such, they do not generate any dynamic HTML or return any static assets like client-side JavaScript, they just return data in JSON strings that developers can use to built the user interface for the app, or simply consume the api's data in an existing app
* REST APIs are useful because they all you to create the back-end of an application only once, and then built many different client-side applications that connect to and consume the API's data
* I.e, you could build an API that is used by an iOS app, Android app, web app, command line tool, or even a desktop application

## CRUD
* CRUD stands for Create, Read, Update, Delete 
* Most applications perform these four operations, including APIs
* In web applications and APIs, CRUD maps to the HTTP verbs POST, GET, PUT, Delete 
    * Create -> POST
    * Read -> GET
    * Update -> PUT
    * Delete -> DELETE 
* When a client application wants to retrieve data, it will make a GET request 
* When it wants to add something to a data store, it will make a POST request 
* When it wants to update existing data in a data store, it will make a PUT request
* When it wants to delete a record from a data store, it will make a DELETE request

## Set up
* Setting up an API in Express is not much different from setting up a regular website or web application. The main difference is that the API will receive, and send out, JSON rather than anyting else
* In your `app.js` file you will need to use Express middleware to parse all incoming HTTP POST requests with JSON payloads:

```js
app.use(express.json());
```

## Sending responses 
* Responses are sent as JSON. Therefore, you must use the `response.json()` method rather than the `send` or `render` methods
* Example of a REST route: 

```js
router.get('/quotes', asyncHandler(async (req, res) => {
    const quotes = await records.getQuotes();
    res.json(quotes);
}));
```

* `response.json` accepts a JavaScript object and stringifies it before sending it to the client as a JSON string
* You won't always want to actually send anything back on the `response` object. For instance, when a client application sends a DELETE or PUT request you may want to just have your API carry out the operation and not return any JSON. To do this, send the status code 201 - No Content with the response and then end the response: 

```js 
res.status(201).end();

```

## Receiving client data
* Receiving and working with data sent with the request is the same as it is with a standard Express app. As long as you have JSON middleware running, the JSON sent from the client will be parsed into a JavaScript object and made available on the `request.body` property
* POST route example: 

```js
router.post('/quotes', asyncHandler(async (req, res) => {
    if (req.body.author && req.body.quote) {
         const quote = await records.createQuote({
              quote: req.body.quote,
              author: req.body.author
         });
         res.status(201).json(quote);
    } else {
         res.status(400).json({ 
              message: "400 Bad Request. Quote and author required" 
         });
    }
    
}));
```

## Error handling
* It is an essential practice to account for, and handle any foreseeable errors that could take place
* In a REST API you will be interacting with a data store or a database of some kind, which is typically an asynchronous process. Using `try...catch` blocks is a good idea, or if you are going to have a lot of routes you may want to use a piece of middleware that will automatically wrap your route callbacks in a `try...catch` block for you:

```js 
function asyncHandler(cb){
    return async (req, res, next)=>{
         try {
         await cb(req,res, next);
         } catch(err){
         next(err);
         }
    };
}
```

* The function above can then be used as the callback to a route, and the asynchronous processes can be passed as a callback to the handler function:

```js 
router.get('/quotes/:id', asyncHandler(async (req, res) => {
    const quote = await records.getQuote(req.params.id);
    if (quote) {
         res.json(quote);
    } else {
         res.status(404).json({ message: "404 - Not Found" });
    }
}));
```

* There is also an npm module called [express-async-handler](https://www.npmjs.com/package/express-async-handler) that will do this for you
* By default, Express will send an HTTP status code of 200 - OK, but if you are handling errors you will want to set the status code to the appropriate code with `response.status()` which accepts a code
* For instance, if the request from the client omits necessary information the server needs to perform the given operation, you would use 400 - Bad Request
* [MDN HTTP Status Codes documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
* To jump control flow to Express's built-in error handler, or to a custom error handler you have written, pass anything to the `next()` function, for instance a thrown error
* It is a good idea to have your own global error handler in your `app.js` file that will process and handle any errors, either errors that are passed to `next` in the `catch` block of your route or by the middleware handling your error catching, or that arise from a request for a non-existent resource 
* The following example shows a middleware that will instantiate a new error and pass it to `next` if the request matches no routes, and then a middleware that fill send an error message to the client: 

```js 
// Throw error and pass to next on no matching route 
app.use((req, res, next) => {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
});

// 404 Error
app.use((err, req, res, next) => {
     res.status(err.status || 500);
     res.json({
          error: {
               message: err.message
          }
     });
});
```

* Without your own custom error handling middleware such as the one above, when `next` is called with an error passed to it, Express will handle it but the output to the client may not be what you want them to see
* Having a middleware that catches requests for non-existent resources is a good idea so a nice 404 - Not Found message can be sent rather simply nothing