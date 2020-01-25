# Express.js Basics
Express is a server-side framework for developing websites, applications and APIs on the Node.js runtime. This set of notes will set out some of the basics but will not go deep, as it is more appropriate to immerse yourself in the [documentation](https://expressjs.com/en/4x/api.html).
This set of notes will assume you understand http basics and commandline fundamentals including npm. I use bash on Ubuntu but the commands I show should work with little modification in other environments.

## What is a framework?
A framework is a reusable library that handles common menial or low-level tasks required to build and run an application so that the developer is able to focus on building the "meat and potatoes" of the app. Rather than write everthing from scratch, the developer can rely on tried and true solutions to common software development problems. Frameworks also often provide some level of structure to an appliction, allowing the developer to leverage the usefulness of design patterns. The most common architectural design pattern for web applications is the MVC - **Model View Controller** pattern, which will be discussed briefly later.

## What does Express do for you?
Express handles things like:

* Routing (How users connect to different parts of your app)
* Middleware (Manipulation of the http request and response objects for any number of reasons)
* Using a template engine to dynamically generate HTML
* Serve dynamic content through a server 
* Serve static files through a static server (html, css client-side JavaScript)

## Setup
* To install Express open the terminal and change into the root of your project folder and run `$ npm install express --save` 
* Then require and initiate Express in your main application file (i.e index.js or app.js)
    
    ```js
    const express = require('express');
    const app = express();
    ```
* If you are going to use Pug templates to render your views (views refers to the pages your users will see), run `$ npm install pug --save` and then set the Express app to use Pug in the main file:
    
    ```js 
    app.set('view engine', 'pug');
    ```
* Then create a folder called `views` in the root of your project. Later when you render views, Express will by default know to find your view files in that folder and you won't have to provide the file path
* You will need an HTTP server at least for development, so add

    ```js
    app.listen(8000, [callback]);
    ```
    to your main file. This is based on Node's `http` server. The 8000
    represents the port number. It could also be 8080 or 3000 if you like.
    You can run an optional callback, usually to display a message in the console while the server is running.

* If you are going to serve static files (i.e html, css, client-side JavaScript - any files that never change) you will need a static server. Create a folder called `public` in your project root and then add:

    ```js
    app.use('/static', express.static('public'));
    ```
* This tells Express to route all static files through the '/static' route and to find them in the `public` directory (named so because it houses files that will be publicly available to your users). Later when you include static files in your views you won't need to specify "public" in the file path becuase Express will automatically serve static files from the `public` folder. You will just need to supply "/static/path/to/file"
* If you are going to handle any form submissions that use the POST http verb then you will need middleware that makes the body of the POST request available to you. Add:


    ```js
    app.use(express.urlencoded());
    ```
* This middleware will parse incoming POST requests with urlencoded bodies and make that body available on the request object for you to use

## Routing
* Most apps and websites have at least a few different pages, or views. All server-side frameworks provide a convenient way to "route" your users to different views of the app or site. There are several slightly different ways to do this.
* The most basic way is to create a route in your main file:

    ```js
    app.get('/', (request, response) => response.send('Hello world!'));
    ```
    When users access the home route '/' they will see "Hello world!" printed to the browser.

* Routes always use an http verb to specify which kind of request the route should look for. In the example above, the route only matches with a GET request. A POST request sent to '/' would result in nothing
* You can use get, post, put, delete, patch and so on
* A more modular way to route would be to make a folder called `routes` in your project root and add route files to it as needed, handling different related sets of routes. For instance, say you're building a website about animals and the site has different pages of information on each type of animal. You may have a set of routes that all start with '/cats', so all those routes go in their own routes file, and all routes that start with '/dogs' go in their own file 
* To make modular routes, create a file in your `routes` folder and first require Express and the Express Router:

    ```js 
    const express = require('express');
    const router = express.Router();
    ```

    Then create your routes with 

    ```js
    router.get(path, callback);
    ```
* The `.send()` method is fine for sending small strings of text or html to the client, but you will more often use `response.render()` which will accept the name of a view file to output:

    ```js
    router.get('/cats', (request, response) => response.render('cats'));
    ```
* Express will look in your `views` folder for a file called `cats.pug` and render it as html to the client
* Finally, in your routes file you must export your routes so they are available in the main file: 

    ```js 
    module.exports = router;
    ```
* You will then require your routes in your main file and pass them into Express middleware. Recall middleware is how you manipulate incoming requests and outgoing responses. Passing your routes into Express middleware passes the incoming request into the routes, where it will match with a route In your app.js or index.js file:

    ```js 
    const catRoutes = require('./routes/catRoutes.js');
    app.use('/cats', catRoutes);
    ```
* Then all incoming requests that match '/cats' will be funnelled into your cat routes. You could also leave out the route at the beginning of `app.use` and allow all routes to be funnelled through 
* If your app or site isn't that big, you may not find it useful to separate routes into separate files. Instead you could put all your routes into a file called `routes.js` and then require it and pass it to Express middleware in the same way. The choice is yours, Express has no opinion on the matter and won't force any particular option on you.

## Middleware
* Middleware has already been referenced a few times. Middleware is just a way to do something with a request or response. It operates in "the middle" of the data pipeline. 
* E.g `http request -> middleware does something -> http response`
* For example, Express requires a middleware function to alter the incoming POST request in a way that makes the body of the request available to the developer
* In Express, almost everything is middleware,including the functions you pass into routes
* To use middleware, you use
    ```js 
    app.use(callback)
    ```
* The middleware callback you supply must take the request, response and next parameters. The `next` parameter represents the next middleware function that is going to be called after the current
* Middleware is called in a chain that ultimately terminates with the sending or an http response, which is why `next` is necessary
* A simple example of middleware:

```js
app.use((request, response, next) => {
    response.locals.msg = "Hi from previous middleware :)";
    next();
});

app.use((request, response, next) => {
    console.log(response.locals.msg);
    next();
});

//console output: Hi from previous middleware :)
```
* In the above example, as the request passes through the first middleware, a variable is attached to the [response.locals](https://expressjs.com/en/4x/api.html#res.locals) property and then next is called, so the next middleware in the chain is called. The next middleware then logs the `msg` variable tacked onto `response.locals` variable out to the console. So the first middleware made an alteration to the http response and then passed it along down the data pipeline where another middleware did something else with it
* There are a lot of applications for middleware and there are many packages that can be used on npm to extend the functionality of your Express app that you can install and use
* You can also write your own middleware. The only requirement is that your custom middleware take the request, response and next parameters and then call the `next()` function to continue the middleware chain:

```js 
const customMiddleware = (req, res, next) => {
    //do this, do that, do whatever you want 
    next();
}
```