# Object-oriented JavaScript

* (note: this note set assumes basic knowledge of JavaScript objects)
* Object-oriented programming is a paradigm that structures software in terms of objects
* Objects are like containers for groups of related functionality in a program
* Objects can be thought of as components of software. Software ideally should be constructed of stand-alone components that do the same thing regardless of the program they are placed in
* Object-oriented programming is a way to make software more DRY, easier to read and understand, and organized in a way that makes it possible for many programmers to work on a large project without having to know and understand the entire code base
* Object-literals are one way to create an object, but they aren't the only way. Objects can be constructed from a class or prototype
* JavaScript is a prototype-based language, but ES6 introduces class syntax
* Packaging properties and methods into an object and assigning that object to memory with a variable is called **encapsulation**.

## Classes

* Instantiating objects from classes allows for the instantiation of countless objects that inherit their properties and methods from a single prototype
* Basic class syntax:

```js

class Pet
{

    constructor(animal, age, breed, sound) {
        this.animal = animal;
        this.age = age;
        this.breed = breed;
        this.sound = sound;
    }
}
```
* The `constructor` function accepts parameters and sets properties, but unlike PHP those properties do not need to already be defined
* (note: the `this` keyword is the conventional way of referring to the object itself. `this.age` refers to the age property of the object in which the `this` keyword is used. `this` has to be used because when defining classes it is not yet known what name an instantiated object will take)
* A constructor is not required inside a class, but is needed for dynamically generating objects
* To instantiate a class:

```js
const dog = new Pet('dog', 1, 'lab', 'woof!');
```
* Parameters for the constructor are passed at instantiation of the object
* To add methods to a class:

```js
class Pet
{

    speak() {
        console.log(this.sound);
    }
}

```

## Getters and setters

* Getters are methods used to return object properties
* Setters are methods used to set object properties
* Setters are used for setting computed properties typically
* Getters and setters are useful because they allow you to include logic when retrieving and setting properties

#### Getters
* Getters return dynamic or computed properties
* E.g:

```js
class Pet
{

    constructor(animal, age, breed, sound) {
        this.animal = animal;
        this.age = age;
        this.breed = breed;
        this.sound = sound;
    }

    // getter logic
    get activity() {
        const today = new Date();
        const hour = today.getHours();
        if (hour > 8 && <= 20) {
            return 'playing';
        } else {
            return 'sleeping';
        }
    }

    speak() {
        console.log(this.sound);
    }
}
```

* In the example above, the getter dynamically generates and returns an activity based on the time of day
* Getters can be accessed on objects using dot or bracket notation, but they are not called in the same way regular functions are. They do not need parentheses
* Getters are useful when you want to return a property that is not always going to be the same, and therefore cannot be set in the constructor
* The return value of a getter is available when the getter is called, but is never actually encapsulated in the object. It is not the same as a property that is saved to memory with a variable

#### Setters

* Setters receive a value and perform logic on that value, then update an existing property with that value, or generate a new property
* Setters always receive exactly one argument that is then used to set a property or generate a new property
* Setters are not called using regular function syntax but instead the function is executed when the property is set using the regular dot notation
* The name of a property on an object can never be the same as the name of a getter or setter. Therefore:

```js
set owner(owner) {
    this.owner = owner;
}
```

* will throw an error
* The property that points to the return value of a setter is called a **backing property** and by convention starts with `_` but then has the same name as the setter:

```js
// Create the setter:
set owner(owner) {
    this._owner = owner;
}

// Call the setter:
object.owner = 'Your Name';
```

* Setters are called using dot notation and appear to work the same as manually setting a property on an object
* The difference is that a setter can contain logic and perform operations on the value it is passed before updating a property with its value
* To retrieve the value of a property set by a setter, you need a getter:

```js
get owner() {
    return this._owner;
}
```
* The value of `this._owner` can then be accessed through regular dot or bracket notation

## Object interaction
* In object-oriented programming, programs are composed of objects interacting with other objects
* An property of an object can contain another object
* For example, in the above example, the owner of the pet is set to a string value with a setter, but we could make a new class representing the owner, instantiate it, and pass the owner object to the pet object to set the owner object as the owner property on the pet object

## Inheritance
* Inheritance is covered in [ES2015.md](ES2015.md)
