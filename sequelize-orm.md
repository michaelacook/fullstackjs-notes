# Sequelize ORM 
* Sequelize is an Object Relational Mapper, which is a library that allows you to interact with a database in the language you work in (i.e, JavaScript in this case) rather than using SQL. These notes will provide the bare bones for getting up and running with Sequelize, but it is highly recommended to read the [official Sequelize documentation](https://sequelize.org/master/)

## Setup
* Install Sequelize using npm `$ npm install sequelize --save`
* Make a directory for handling database concerns and create a base file to handle the database connection and basic configurations
* Require Sequelize and instantiate a database connection:

```js 
// require the Sequelize class
const { Sequelize } = require('sequelize');


// connect to database server
const sequelize = new Sequelize('treehouse', 'root', '', {
    host: 'localhost',
    dialect: 'mysql',
    // global options can be set with 'define' property
    define: {
        // db options
    },
});`
```

* Create an object that contains the Sequelize class, the Sequelize instance, and your models:

```js 
const db = {
    sequelize, 
    Sequelize,
    models: {}
}; 

// here you will add models to your db object with db.models.ModelName = require(path/to/model);
```

* Export the `db` object: `module.exports = db;`

## Defining models 
* You can now start creating models. Models are JavaScript classes that "model" database tables and will be used by your controllers to retrieve data to update application views, as well as save application data
* Start by making a models directory in your database directory
* Name your file by the model noun you are setting up and require the Sequelize package
* Models are classes that extend the `Sequel.Model` class. When defining models it is useful and convenient to export a function that returns an initialized model class
* First, set up your function:

```js 
// accepts the Sequelize instance as an argument
module.exports = (sequelize) => {
    // define and initialize model
}
```

* Next, define your class:

```js 
module.exports = (sequelize) => {
    class Example extends Sequelize.Model{}
}
```

* Once you have defined the class, you must initialize it with the `Sequelize.Model.init()` method. This is where you define the table columns and their data types and any other options:

```js 
module.exports = (sequelize) => {
    class User extends Sequelize.Model{}

    User.init({
        firstName: {
            type: Sequelize.STRING, // data type (i.e Sequelize.STRING)
            allowNull: false, // boolean: allow null insertions 
            validate:, // object: specify options for validation
        }, 
        lastName: {
            // define column2
            type: Sequelize.STRING,
            allowNull: false,
        },
        email: {
            type: Sequelize.STRING,
            allowNull: false
        }
    }, {
        /* this is a configuraqtion object that is passed as the second 
        argument to Sequelize.Model.init(). At minimum this configuration 
        object must include the Sequelize instance*/
        sequelize
    });

    return Example;
}
```

* Once your model is defined and the file exports a function that returns your initialized model, you can require that model in your database connection file: 

```js 
const db = {
    sequelize, 
    Sequelize,
    models: {}
};

db.models.User = require('./models/User.js')(sequelize);

module.exports = db;
```

* In the example above the `Example` model file is required, and since it exports a function, the sequelize instance is passed as an argument. This would be the same as:

```js 
const user = require('/models/User.js);
db.models.User = example(sequelize);
```
* Your `User` model is now available anywhere you require your `db` object. So it could be required in controllers and queried to update your view or save application data sent from the client


## Performing CRUD operations
* Now that your database model is initialized and ready to use, you can insert and query data from it. Since Sequelize is promise-based, most methods for interacting with your database are asynchronous and will require the `async` and `await` keywords 

### Inserting records (Create)
* There are two ways to create a new record. You can use the `Model.create()` method which automatically saves the record, or the `Model.build()` method which creates a representation of a row but does not save it to disk. You must use the `Model.save()` method after calling `build()`
* E.g:

```js
const db = require('./db');
const { User } = db.models;

class SignupController
{
    async signup(request, response, args)
    {
        const firstName = request.body.firstName;
        const lastName = request.body.lastName;
        const email = request.body.email;

        // create new record
        const record = await User.build({
            firstName: firstName,
            lastName: lastName,
            email: email
        });

        // save record 
        await record.save();

        response.redirect('/signup-confirmation');
    }
}
```

* Alternatively, you could use the `Model.create` method:

```js 
await User.create({
    firstName: firstName,
    lastName: lastName,
    email: email
});
```
* This will automatically save the record to disk
* It is a good idea to use the `Sequelize.sync` method before saving records to the database, because it will create the database table and sync it to the database if it doesn't already exist prior to inserting your record: 

```js 
...
    async signup(request, response, args)
    {
        const firstName = request.body.firstName;
        const lastName = request.body.lastName;
        const email = request.body.email;

        // sync if table doesn't exist 
        await User.sync();

        // create new record
        const record = await User.build({
            firstName: firstName,
            lastName: lastName,
            email: email
        });

        // save record 
        await record.save();

        response.redirect('/signup-confirmation');
    }
    ...
```

### Fetching records (Read)
* To find a single record by it's primary key (Sequelize will automatically add an auto-incrementing primary key column called `id`) use `Model.findByPk(primaryKey)`;
* To find the first matching single record using a where clause, use `Model.findOne({options})`
* E.g: 

```js 
const movieByRuntime = await Movie.findOne({
    where: {
        runtime: 115
    }
});
```
* In the `where` object passed in the `options` argument, you can specify criteria to filter records 
* Find all matching records with `Model.findAll({options})`. This works the same way as `findOne` 
* It is possible to specify only specific columns to retrieve with the `attributes` option:

```js
const titlesAndRuntimes = await Movie.findAll({
    attributes: ['title', 'runtime'],
    where: {
        // where condition(s),
    }
}); 
```
* You can also specify the order of records as descending or ascending, as in SQL with the `order` option:

```js 
const orderedMovies = await Movie.find({
    attributes: ['id', 'title'],
    where: {
        // where condition(s),
    },
    order: [
        ['id', 'DESC']
    ]
});

```
* The `order` property is an array of arrays because you could order by multiple columns and each internaarray represents the attribute you want to order by and either DESC or ASC for descending or ascending

### Updating records (Update)
* There are two ways to update a record. You can queries the database and find the record you want to change, change it and then use `Model.save()` or you can use `Model.update()`
 
#### `Model.save`
* Find the record, alter, then call the `save` method:

```js 
const user = await User.findOne({
    where: {
        email: 'email@email.io'
    }
});

user.email = 'e_mail@email.com`;

await user.save();
```

* Find a record and update it with `Model.update()`:

```js 
const user = await User.findOne({
    where: {
        email: 'email@email.io'
    }
});

await user.update({
    email: 'e_mail@email.com`
});
```

* You can specify the columns you want to update with the `fields` property in the optional options object. This will prevent any other columns from being updated, even if `update` tried to alter them: 

```js 
const user = await User.findOne({
    where: {
        email: 'email@email.io'
    }
});

await user.update({
    email: 'e_mail@email.com`
}, { fields: ['email'] });
```

## Deleting records (Delete)
* To delete a record, query the database to find the record you want to delete, and then call the `Model.destroy` method on it 

```js 
const toyStory3 = await Movie.findByPk(3);
await toyStory3.destroy();

```

* you have the option to use paranoid mode which allows soft deletes. the record is not deleted from disk but is not returned in future queries. to do this, add  paranoid: true to the options argument when your model is initialized:

```js 
module.exports = (sequelize) => {
    class User extends Sequelize.Model{}
    User.init({

    ...

    }, { // this is the options object passed to init
        sequelize, 
        paranoid: true, // soft deletes
    });

    return User;
}
```

## Using operators in queries
* In order to use logical and numerical operators in queries, you must first require Sequelize's `Op` class:

```js 
const { Op } = require('sequelize');
```

* You can then use operators to specify conditions nested inside the `where` property in a query with the following syntax:

```js 
const record = await Model.findAll({
    where: {
        column: {
            [Op.operator]: rightOperand
        }
    }
})
```

* This syntax can feel a bit unnatural. Here is a concrete example:

```js 
const movies = await Movie.findAll({
    where: {
        releaseDate: {
            [Op.gt]: 1984
        }
    }
});
```

* The above query retrieves all movie records where the release data is greater than 1984. `[Op.gt]` stands for the greater than `>` symbol and translates to `releaseDate > 1984`
* Read the docs for all [Sequelize operators](https://sequelize.org/master/manual/model-querying-basics.html#operators)

## Next steps
* This set of notes is a really basic primer and leaves a lot out. It is just enough to get you up and running. Read the [documentation](https://sequelize.org/master/manual/getting-started.html) starting with the Getting Started page and work through the examples to develop your understanding