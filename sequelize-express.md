# Using Sequelize with Express.js
* Integrating Sequelize into an Express project is pretty straight forward, and using the [Sequelize CLI](https://github.com/sequelize/cli) and [Express app generator](https://expressjs.com/en/starter/generator.html) 
* To set up a database project with Express and Sequelize:

1. Create your project's root folder. In the terminal change into your project folder and run `$ npx express-generator`. This will create the following file directories in your project folder: 
    * `bin` - This is for the executable file(s) of your application
        * `www.js` - This is the executable file (the main entry point) for your application. It is populated with code that starts up a server and requires the `app.js` file
    * `public` - This is for any publicly available resources for your application (i.e client-side JavaScript, CSS files, HTML files, etc)
        * `images`
        * `javascripts`
        * `stylesheets`
    * `routes` - This is the folder where all your route handlers will be 
        * `index.js` - This is the file that will require all your routes and export the router
        * `users.js` - This is a dummy route file. Delete it is it isn't useful to you 
    * `views` - This is where your views will go (Pug templates)
    * `app.js` - This is the file that instantiates the Express app, sets up some basic middleware, and sets up 404 and error handling middleware, then exports the `app` variable
2. Once you have a project structure set up, run `$ npm install sequelize-cli --save-dev`. Then to initialize your Sequelize project, run `$ npx sequelize init`
3. Install Sequelize: `$ npm install sequelize --save`
4. Once Sequelize is installed, run `$ npx sequelize init` to initialize a Sequelize project. This will create the following directories: 
    * `config` - Basic configurations
        * `config.json` - This file contains basic configurations for development, testing, and production environments. You will need to edit it for your own needs
    * `migrations` - Database migrations
    * `models` - This is where your table models will go 
        * `index.js` - This file is populated with boilerplate code to instantiate the Sequelize module, automatically require all your model files, and export a variable called `db` that contains the Sequelize class and instance as well as your models.
    * `seeders` - Seed files
5. You are ready to start building out your project! This set of notes will not reiterate much of what the [other Sequelize noteset](sequelize-orm.md) covers. Understanding the directory and file structure of an MVC app with Express and Sequelize plus a basic understanding of using Sequelize should be enough to get you up and running

#### How do all the files and folders relate to each other? 

```
// Express MVC app structure

.
├── bin
|   ├── www.js
├── config
|   ├── config.json
├── migrations
├── models
|   ├── index.js
├── node_modules
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
├── routes
|   ├── index.js
├── seeders
├── views
├── app.js
├── package-lock.json
└── package.json
```

* In a server-side application you have a few main components that make the application work, and a bunch of supporting components. The main components are the models, routes, and the executable
* `app.js` instantiates Express, sets up middleware, and passes the `request` and `response` objects through the middleware and into the routes, which it requires from the routes index. It then exports the `app` component
* The routes handle incoming HTTP requests and send out the correct responses, which could be views and static files from the `views` and `public` folders, or JSON strings in the case of a REST API. All the routes are exported together in the `index.js` file in the `routes` folder 
* The `www.js` executable requires the `app` module and sets up an HTTP server to listen for requests. It also synchronizes your Sequelize models to the database. When `www` is executed it listens for HTTP requests and when one comes it, it is handled by the `app` module, which contains all the middleware and routes 

## Define models 
* Once you understand how to set up Express and Sequelize together and how the different files and directories relate to each other, you can start defining your models. This was covered in the other noteset. Here is an example of an Article model for a blog app: 

```js 
'use strict';

const Sequelize = require('sequelize');
const moment = require('moment');


module.exports = (sequelize) => {
    class Article extends Sequelize.Model
    {
        publishedAt()
        {
            const date = moment(this.createdAt)
                .format('MMM D, YYYY, h:mma');
            return date;
        }


        shortDescription()
        {
            const shortDesc = this.body.length > 200 ? this.body.substr(0, 200) + '...'
                : this.body;
            return shortDesc;
        }
    }

    Article.init({
        title: {
            type: Sequelize.STRING,
            validate: {
                notEmpty: {
                    msg: '"Title" is required.',
                }
            },
        },
        author: Sequelize.STRING,
        body: Sequelize.TEXT
    }, { sequelize });

    return Article;
}
```
* The methods defined in the model are called [**instance methods**](https://sequelize.org/v5/manual/models-definition.html#expansion-of-models) and they are available on all instances of the model. They can be called directly in pug files to modify data 

## Using models in routes 
* Using models in routes is simple. Since Sequelize is promise-based, using `async` and `await` is the best option for handling database calls inside your routes. If you have a lot of routes, a package like [express-async-handler](https://www.npmjs.com/package/express-async-handler) which wraps your routes in a `try...catch` block is useful
* E.g:

```js 
const express = require('express');
const router = express.Router();
const Article = require('../models').Article;
const asyncHandler = require('express-async-handler');


/* GET individual article. */
router.get("/:id", asyncHandler(async (req, res) => {
  const article = await Article.findByPk(req.params.id);
  if (article) {
    res.render("articles/show", { article: article, title: article.title }); 
  } else {
    res.sendStatus(404);
  }
}));
```

## Getting help 
* It is important to read the documentation for both Express.js, Sequelize, and the Sequelize CLI. The more you work with these tools and read their technical documents, the more you will understand them and become fluent with them.