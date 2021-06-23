# Data Relationsihps with SQL and Sequelize 

## Understand Data Relationships
* When designing a database for an application it is a good practice to start by listing all the nouns that the application will be working with 
* E.g for an application like IMDb you may need the nouns Movie and Person, each with their own attributes (table columns)
* It is best to set up these nouns so that data is not repeated between them and only referenced by PK-FK relationships
* Separating data into separate but related tables helps prevent data from being repeated and using large amounts of disk storage
* E.g If we added a directorFirstname and a directorLastName columns to the Movie table, then the same director's name could be repeated many times in the Movie table. Instead if we have a Person table with each person's name and role (actor, director, writer etc) only once, then we can reference that row many times with a numeric foreign key and save a lot of space
* This not only saves space but also increases the ease of updating information. The data only need to be updated in a single place as opposed to hundreds thousands or maybe even millions
* The associations between the data in your tables (nouns) are data relationships, which can take the form of one-to-one, one-to-many, and many-to-many
* The relationship between directors and movies is a one-to-many relationship: one person can be the director for many movies, but each movie can have only one director

## Data Relationships in Sequelize Models
* Sequelize refers to data relationships as **associations**
* [Associations documentation](https://sequelize.org/v5/manual/associations.html)
* There are a variety of association types you can use in Sequelize, which is determined by the type of relationship that exists between your data
* When you define an association or relationship on a model to another model in Sequelize, Sequelize will automatically add a foreign key to the model's associated table
* The relationship between one table to another depends on whether you look at that table as the "source" table or the "target" table
    * From the point of view of the Person table as the "source" table, it has a one-to-many relationship with the Movie table, since one Person can be the director for many movies 
    * From the point of view of the Movie table as the "source" table, it has a one-to-one relationship with the Person table, since a single movie can have a single director (for our purposes here. In reality its possible to have more than one director)
    * Sequelize allows you to define the "one" and the "many" side of the relationship as an association on each model, and this provides flexibility
* To add an association to a model, you define the `Model.associate` method on the model class. It takes an instance of the models object created in your index file, and then calls an association method on the model class that accepts the specific model to which the association is being defined. E.g: 

    ```js
    module.exports = (sequelize) => {
    class Person extends Sequelize.Model {}
    Person.init({
        id: {
          type: Sequelize.INTEGER,
          primaryKey: true,
          autoIncrement: true,
        },
        firstName: {
          type: Sequelize.STRING,
          allowNull: false,
        },
        lastName: {
          type: Sequelize.STRING,
          allowNull: false,
        },
      }, { sequelize });

      // this is where the association is defined
      Person.associate = (models) => {
          Person.hasMany(models.Movie)
      };

      return Person;
    };
    ```
* The `Model.associate` method is called in the index file after all the models are imported:

    ```js
    // associations are made if any are defined, after the models are imported

    Object.keys(models).forEach((modelName) => {
      if (models[modelName].associate) {
        console.info(`Configuring the associations for the ${modelName} model...`);
        models[modelName].associate(models);
      }
    });
    ```
* The `hasMany` association tells Sequelize that the `Person` model can be associated with one or more movies
* The `Movie` table will now have a `PersonId` foreign key added to it
* To add the one-to-one relationship between `Movie` and `Person`, which is the relationship from the point of view of the `Movie` table as the source table, you use the `Model.belongsTo` method inside the `Model.associate` method: 

    ```js
    Movie.associate = (models) => {
      Movie.belongsTo(models.Person)
    }
    ```
* This tells Sequelize that a movie can be associated with only one person

## Refine Sequelize Associations
* The way the relationship is defined between `Movie` and `Person` has some small problems that can be fixed by passing an optional options object to the `Model.associate` method
* Currently the `PersonId` foreign key on `Movie` doesn't have a name that actually tells us anything about a `Person`'s relationship with a `Movie` and it doesn't match the naming convention of other columns on the model. The `PersonId` foreign key can also be `null` which we do not want
* To customize the foreign key name pass an options object to each association definition on each object specifying the foreign key name:

    ```js
    Movie.associate = (models) => {
      Movie.belongsTo(models.Person, { foreignKey: 'directorPersonId' });
    };
    ```

    ```js 
    Person.associate = (models) => {
      Person.hasMany(models.Movie, { foreignKey: 'directorPersonId' });
    };
    ```
* It is important to add these options to both sides of the relationship (on both models) or Sequelize will not know that each association is describing the same relationship
* Now the foreign key added to `Movie` has a name that provides context and is continuous with the naming convention used for other column names in `Movie`
* To configure the nullability (whether the foreign key can be null) you need to replace the value of the `foreignKey` options property with an object that defines the `fieldName` for the foreign key name, **and** `allowNull` to `false`:

    ```js 
    Movie.associate = (models) => {
      Movie.belongsTo(models.Person, {
        foreignKey: {
          fieldName: 'directorPersonId',
          allowNull: false,
        },
      });
    }
    ```

    ```js
    Person.associate = (models) => {
      Person.hasMany(models.Movie, {
        foreignKey: {
          fieldName: 'directorPersonId',
          allowNull: false,
        },
      });
    }
    ```

* Now the `directorPersonId` foreign key on `Movie` cannot be null
* Again, to configure nullability both sides of the relationship need to be configured

## Retrieving Related Data 
* Once you have an association set up with a primary key and foreign key relationship, the foreign key in one table will reference the primary key in the other, but the foreign key by itself will probably not be very helpful as it will just be a number
* For instance, querying the Movie table will give you a data set including a foreign key number referencing the director in the Person table. But you want to know the name of the director, not the foreign key number
* Instead of having to perform two separate queries to get the value of the foreign key in the primary table, Sequelize gives you a way to do this in a single query
* To do this, you pass an options object to your query with the `include` property set to an array of objects. Add an element to the array containing a `model` property and the model name for which you want any related data: 

  ```js 
  const movies = await Movie.findAll({
    include: [
      {
        model: Person,
      },
    ],
  });
  console.log(movies.map(movie => movie.get({ plain: true })));
  ```
* For each model associated with the model you are querying, you can specify to include any related data in the `include` array
* The act of querying multiple models in a single query is called **eager loading** and is the equivalent of creating a join query in plain SQL
* This however, will just add an object onto the data returned in your query named as the model name you are eagerly loading and this does not give any context
* To alias the data returned from eager loading as something with meaning, you can add the `as` property to the association method options object and specify an alias: 

  ```js 
  Movie.associate = (models) => {
    Movie.belongsTo(models.Person, {
      as: 'director', // alias
      foreignKey: {
        fieldName: 'directorPersonId',
        allowNull: false,
      },
    });
  }
  ```

  ```js 
  Person.associate = (models) => {
    Person.hasMany(models.Movie, {
      as: 'director', // alias
      foreignKey: {
        fieldName: 'directorPersonId',
        allowNull: false,
      },
    });
  }
  ```

* This must be done on both sides of the association relationship 
* You can then add the alias to your query in the `include` property: 

  ```js
  const movies = await Movie.findAll({
    include: [
      {
        model: Person,
        as: 'director',
      },
    ],
  })
  ```

* The data you are eagerly loading will now have context and be discernible to a client 
* It is important to give your data appropriate context, especially when you are building a RESTful application or API that developers will be interacting with and would otherwise not know the intentions behind the data naming conventions unless they are made to be self-explanatory

* The ability to retrieve related data applies to both sides of the association relationship. You can get the related data the same way whether you are querying the model for either side 
* E.g: 

  ```js 
  const people = await Person.findAll({
    include: [
      {
        model: Movie,
        as: 'director',
      },
    ],
  })
  ```

* Not only can you get a movie and it's associated director, you can also get a director and get their associated movie(s)
* This is one of the reasons it is important to create associations in your models for both sides of the relationship, as it gives the flexibility to retrieve related data on both sides, reducing the number and complexity of queries you need to perform

