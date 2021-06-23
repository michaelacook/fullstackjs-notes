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

## Processing data from the `request` object
* Data can be sent to the server in a number of different ways, including in the body of a POST request, in url parameters, or in query strings attached to the url

### POST body
* As was already covered, in order to access the POST request payload or body, you must add the correct middleware to parse the post request body:

    ```js 
    // middleware for parsing http payloads
    app.use(express.urlencoded());
    ```
* Once the middleware is installed, the body of a POST request is available via the `request.body` property and will be a key-value pair

### Url parameters 
* url parameters can be specified in a route using the following syntax:

    ```js 
    app.get('/users/:[nameOfParam]' ...)
    ```
* Whatever variable is passed in the url is then available on the `request` object through the `request.params` property. E.g:

    ```js
    app.get('/users/:name' ...);

    const name = request.params.name;
    ```

### Query strings
* Query strings are key-value pairs of variables attached to a url that are useful for sending non-sensitive data to the server
* They take the form of `?key=value[&key=value]` at the end of a url 
* E.g: `http://www.yourSite.com/?search=this+is+a+search+query`
* Query strings are available on the request object via the `request.query` property
* E.g:

    ```js 
    // /shoes?size=10&style=runner

    const query = request.query;
    const shoeSize = query.size;
    const style = query.style;
    ```

## Cookies 
* Cookies are a way to persist data between reloads 
* Since HTTP is a stateless protocol, meaning it does not store anything about the what the user is doing on the site, it is sometimes necessary to use cookies so that the server can "remember" information about a user
* Cookies are stored on the client's computer and are sent along with each http request so the server can read a client's cookies 
* Cookies can be set on the `response` object using `response.cookie(key, value)`
* E.g:

    ```js
    router.post('/hello', (req, res) => {
        const username = req.body.username;
        res.cookie('username', username);
        res.redirect('/');
    });
    ```
* In the above example a username is retrieved from a POST request, and then a cookie is sent to the client on a redirect. Then each time the client makes a request, that cookie will come along with the request and the server will be able to know who the user is by reading the cookie
* To read a cookie, you first must install cookie parsing middleware, such as [cookie-parser](https://www.npmjs.com/package/cookie-parser)
* Once cookie parsing middleware is installed and added to the application, you can get the values from a cookie sent to the server via the `request.cookies` property
* E.g:

    ```js 
    response.cookie('username', 'superman');

    console.log(request.cookies.username);
    // superman
    ```

## Going further 
* So far everything in these notes has been fairly surface level. It is enough to get started, but Express offers many methods on the `request` and `response` objects as well as many other features. Reading through the documentation is important 

## MVC pattern 
* For some reason one topic that doesn't seem to ever covered by the Express Basics course is the Model View Controller pattern. This is an extremely common (and very effective) architectural pattern in which the views (what your user interacts with), models (the "business logic" of the app, i.e database calls, application logic, etc) and controllers (components that handle incoming requests, send data to the correct models, then update the views) are all modular and separate 
* In the MVC pattern, your views don't know anything about any other part of the app. They exist by themselves, and simply have placeholders where data can be subbed in, since they are going to be templates. However, they are completely agnostic about where that data comes from 
* Your controllers are components that receive the incoming request, tell the correct models to retrieve data or to perform some business logic, and then they send the response back out after updating it. But they are completely separate from the models. They don't handle any business logic, just the receiving of information and updating the views
* Your models are components that do the business logic and make database calls, and they are completely separate from views and controllers. They are interact with controllers by sending data to them, but they are completely agnostic about the controller. All they do is perform business logic and then hand that data off to whatever controller calls for it 
* Let's use an example to think about this. Suppose you have a lorem ipsum generator, which creates random nonsense Latin text for designers to use to demonstrate their layouts. Your app could be broken down like this: 

    1. `LoremIpsumGenerator` - a component that takes a number of paragraphs it should produce, and produces the text. It doesn't care how it gets that number, and it doesn't care where the data goes after it produces it. It's only role is to generate the placeholder text. This is a model, the 'M' in MVC.
    2. A page that displays the text. It has a placeholder where the data is subbed in to be displayed by the user. It doesn't care where the data comes from, it just takes the data and displays it visually. This is a view, the 'V' in MVC.
    3. `LoremIpsumController` - a component that receives the http request, which includes input from the user for how many paragraphs to generate. It parses that request, sends it to `LoremIpsumGenerator` and then gets back the data from the model, then updates the view before passing off the `response` object to the application router. It is the controller, the 'C' in MVC

* Using the MVC pattern makes your codebase more easy to read by implementing a principle called the "separation of concerns" - each component of an app should do exactly one thing (or one set of closely related things) and not be involved in anything else
* This makes you app modular, more scalable, and more easy to read by keeping things organized and separate 
* There are many ways to implement the MVC pattern and you should do research and read about this topic for yourself. Personally, I like the modularity and simplicity of object-oriented programming. OOP makes implementing MVC easy in my opinion. This is how I do it: 

1. Make a folder for controllers 
2. Make a folder for models 
3. Make a folder for views (you should already have this)
    
* Following the lorem ipsum example, make a file called `LoremIpsumGenerator.js` and make a new model class in it like this: 

```js 
module.exports = class LoremIpsumGenerator
{
    // add methods needed to make the lorem ipsum text
    makeText(paragraphs) 
    {
        // make the text
    }
}
```

* Make a new controller class called `LoremIpsumController.js`: 

```js 
const loremIpsumGenerator = new (require(./path/to/model));

module.exports = class LoremIpsumController
{
    render(request, response)
    {
        const numberOfParagraphs = request.query.paragraphs;
        const loremIpsumText = loremIpsumGenerator.makeText(paragraphs);
        response.render('loremIpsumView.pug', { loremIpsumText });
    }
}
```

* Make your view template in the `views` directory 
* Require and instantiate your controller in your routes file and call the controller's `render` method in the route:

```js 
const loremIpsumController = new (require(/path/to/controller));

router.get('/loremIpsum', (req, res) => loremIpsumController.render(req, res));
```
* As you can see, this keeps everything separate: the view is completely separate from the controller, which gets data and updates the view, and the model, which receives data and does the business logic. They interact with each other, but each one only handles it's own separate tasks. This will make your programs easier and smoother to make, cleaner, easier to read for yourself and others. You will no doubt also be required by a future employer to use some implementation of this design pattern 