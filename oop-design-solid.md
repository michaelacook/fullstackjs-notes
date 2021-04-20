# Object-Oriented Design and SOLID
- This set of notes covers the basic principles of encapsulation, abstraction, inheritance and polymorphism as well as SOLID design principles 

## Four Concepts 
- [What are four basic principles of Object Oriented Programming?](https://medium.com/@cancerian0684/what-are-four-basic-principles-of-object-oriented-programming-645af8b43727)
- Encapsulation 
  - [Object-oriented programming in JavaScript #4. Encapsulation](https://medium.com/@viktor.kukurba/object-oriented-programming-in-javascript-4-encapsulation-4f9165cd26f9)
- Abstraction 
- Inheritance 
- Polymophism 
  - [Object-oriented programming in JavaScript #3. Polymorphism](https://medium.com/@viktor.kukurba/object-oriented-programming-in-javascript-3-polymorphism-fb564c9f1ce8)

### Encapsulation 
- Encapsulation refers to the ability to hide, or **encapsulate** the implementation of a functionality or data behind an interface like a class or a method 
- Data and functionality can have restricted access 
- An object's state is encapsulated, and therefore hidden 
- Developers do not need to know how the functionality of an object is implemented to use it
- JavaScript has no real ability to restrict access to data or methods, so developers mimick private or restricted access with a customary underscore prepended to the variable or method 

- Example in JavaScript: 

```js 
class MovieCollection {
  constructor(movies, metaData) {
    this._movies = movies ? movies : []
    this._metaData = metaData ? metaData : []
  }

  // this._movies is encapsulated and retrieved only with a getter
  get collection() {
    return this._movies
  }

  addMovie(movie) {
    if (typeof movie !== object) {
      throw new Error("movie must be an object")
    }
    this._movies.push(movie)
  }
}
```

- Example in PHP: 

```php 
<?php

class MovieCollection 
{
    // these variables are private. completely encapsulated and not visible outside the object
    private $movies; 
    private $metaData; 

    // publicly available method
    public function getCollection()
    {
        return $this.movies;
    }

    __construct($movies, $metaData) 
    {
        $this.movies = $movies ? $movies : [];
        $this.movies = $metaData ? $metaData : [];
    }

    // publicly available method
    public function addMovie(Object movie)
    {
        array_push($this.movies, movie);
    }

}
```

### Abstraction
- Abstraction refers to creating an entity that has no concrete instantiation 
- Abstract classes are classes that represent a concept without implementation, and cannot be directly instantiated
- Interfaces are similar to abstract classes, except that in abstract classes heritable methods as well as abstract methods with no implementation can be written, but in interfaces only abstract methods can be written 
- Abstraction is a way to create templates for future classes without specifying how they may implement functionality, only that they must implement functionality 
- JavaScript does not support abstract classes directly. They can be created with a work-around: 

```js 
class Animal {
  constructor() {
    if (this.constructor === Animal) {
      throw new TypeError("Abstract class "Animal" cannot be instantiated directly")
    }
  }
}
```

- Example in PHP: 

```php
<?php

abstract class Animal 
{
    abstract protected function getVocalization();
    abstract public function move();

    public function makeSound()
    {
        echo $this->getVocalization();
    }
}
```

- the Animal abstract class requires that all child classes implement a `getVocalization` method, and a `move` method
- All child classes will also have a `makeSound` method 
- However, Animal doesn't specify how each child class implements these, leaving it up to them to implement it the way it makes sense to do so in their case

## Inheritance 
- Inheritance refers to the ability of one class to inherit from, or extend, another class
- It expresses an "is a" or "has a" relationship between two classes
- In many programming languages, one class inherits from another using the `extends` keyword
- While a child class can extend a parent class in "Is A" inheritance (e.g class B "is a" class A), inheritance can also take the form of composition, where a class is composed of methods from another class 
- Example of inheritance through extends: 

```js 
class Person {
  constructor(name, age) {
    this._name = name 
    this._age = age
  }

  sayHi() {
    console.log(`Hi! My name is ${this._name} and I am ${this._age} years old`)
  }
}

class Programmer extends Person {
  constructor(name, age, lang) {
    super()
    this._language = lang
  }
}
```
- In this example, `Programmer` extends `Person` and before `this` on `Programmer` can be accessed, the `super` function must be called, which calls `Person`s constructor. It can be extend `Person` 
- Example of inheritance through composition: 

```js 
class Programmer {
  constructor(name, age, lang) {
    this._name = name 
    this._age = age 
    this._language = lang 
    this._person = new Person(this._name, this._age)
  }

  greet() {
    this._person.sayHi()
  }
}
```
- In this example, `Programmer` is in a "Has A" relationship with `Person` and has inherited one of it's methods. It does not extend `Person` but is composed from it
- Both types of inheritance have value, but composition can reduce coupling and create flexibility
- Composition allows for multiple inheritance, where as extending a class does not

## Polymorphism
- Polymorphism refers to the ability for a single class to take many forms as it is extended by child classes that implement and extend it's methods 
- All children of a class will have the same type as the parent class, but may override methods and extend functionality when the class extends the parent
- Polymorphism is the ability of multiple objects to implement the same functionality that can work in different ways but supports a common interface and can therefore be swapped for one another
- One interface, multiple implementations
- For instance, a `Shape` class that has an `area()` method, and each new shape class that extends `Shape` implements its own `area()` method
- Polymorphism is not based only on inheritance. It is possible to have many objects that all implement the same interface without extending or composing from a common parent