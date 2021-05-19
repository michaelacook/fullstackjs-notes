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

- Summary: 
  - Encapsulation: Groups related variables and functions into an object 
  - Abstraction: Hides details and complexity to create a simple interface and reduce the impact of change 
  - Inheritance: Eliminates redundant code by having similar kinds of objects inherit properties and methods from a shared "parent" 
  - Polymorphism: Minimizes long conditionals by allowing objects with methods of the same name to behave differently for each object
  - [How to explain object-oriented programming concepts to a 6-year-old](https://www.freecodecamp.org/news/object-oriented-programming-concepts-21bb035f7260/)

### Encapsulation 
- Encapsulation refers to the ability to package, or **encapsulate** the implementation of a functionality or data behind an interface like a class or a function
- Stated another way, encapsulation is when you package up related functionality of a program into an object, and only expose what is necessary for a client (another developer, another program, object, or function)
- Data and functionality can have restricted access. In other words, the **state** of an object is internal and not available to the outside and clients using the object cannot direct access internal state. They can only interact with the object through specified public methods
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
- [YouTube video explaining the difference between abstraction and encapsulation](https://www.youtube.com/watch?v=57SIpmA3vcg&ab_channel=BizofTech)
- Primarily, in OOP design abstraction refers to implementation of logic or functionality being represented with an abstraction, usually a high-level method name that serves as an interface between the client and the actual implementation. The client only interacts with the interface (not an Interface contract, but a representation)
- Similarly, when you use a coffee machine you don't interact with the hardware directly. There is an outer interface that abstracts away what the coffee machine is doing internally 
- The `fetch` API provides a simple interface for making HTTP requests. It abstracts away the lower level functionality of creating and sending the HTTP request
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

# SOLID 
- SOLID is an acronym for five fundamental principles of software development
- They stand for: 
  - Single Responsibility Principle 
  - Open-Closed Principle 
  - Liskov Substitution Principles 
  - Interface Segregation Principle 
  - Dependency Inversion Principle
- Each of these is explored in this section 

## Single Responsibilty Principle 
- [Wikipedia article - good summary](https://en.wikipedia.org/wiki/Single-responsibility_principle)
- A class or module should only have "one reason to change" 
- Stated differently, a class or module should only be responsible for one component of an application 
- Every class, function, module, and directory should have responsibility for a single part of the program's functionality 
- Formulated by Robert C. Martin "Uncle Bob" in the book Principles of Object Oriented Design
- The SRP is based on the principle of cohesion - that related code should occupy a single unit of software
- A responsiblilty is a "reason to change" and is actually about people - who needs it to change? Only one person, or organization, should need a single module or class to change 
  - i.e. only the accounting division needs the function `calculateTax` to change, no other department of the company
  - Code that performs business tasks for different sections of a company should be separated out and grouped together by who needs it to change
- If a verbal description of a class includes the word 'and', it is a sign that the class should be refactored into several independent classes 
- Good application of the SRP ensures loose coupling of the components of software and prevents WET code and ensures that one division of the company's requests for a change don't impact the function of another department of the company
- [article by Uncle Bob](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)
  - Code that changes for the same reasons should be grouped together 
  - Things that do not change for the same reasons should be separate from one another, so that a change to one part doesn't break the other 
  - What this is really about is cohesion and coupling
- Cohesion vs Coupling 
  - [Cohesion and Coupling: The Difference](https://enterprisecraftsmanship.com/posts/cohesion-coupling-difference/)
  - **cohesion**: the degree to which a part of a code base represents an atomic, logically singlular element
    - In other words, cohesion is (truly) **related** parts being put together in a single unit 
  - **coupling**: the degree to which a single unit of code is independent from others
- A code base should have high cohesion, low coupling. Meaning that the various components or units of software should be completely independent of one another, and each unit should be logically singular
- In other words, parts of a code base that are truly related should be kept in one place, and none of them should be aware of other modules, classes or components
- It is impossible to completely decouple a codebase without damaging it's coherence 
- It is also impossible to create perfect cohesion without introducing high coupling 
- A balance between the two is the key to good code, which should have high (but not complete) cohesion and low (but not zero) coupling
- To create a cohesive code base, boundaries between classes, modules, and directories, should reflect the semantics of their contents 
  - This means boundaries between units of code should reflect their intended purpose
- Destructure decoupling: attempting to decouple code so much that it leads to a lack of coherence
  - In other words, code that is related and actually should form a single unit is separated out into separate classes or modules 
  - This code is hard to read and understand
  
## Open-Closed Principle
- [wikipedia article](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)
- Classes should be **open for extension** but **closed for modification** 
- This means that if you need to modify a class to extend it's functionality, you're violating the principle
- **Meyer's open-closed principle** (1988)
  - a module is open if it is available for extension 
  - a module is closed if it is available for use by other modules
  - "A class is closed, since it may be compiled, stored in a library, baselined, and used by client classes. But it is also open, since any new class may use it as parent, adding new features. **When a descendant class is defined, there is no need to change the original or to disturb its clients**." (emphasis added)
- **Polymophic open-closed principle** (1990s)
  - implementations of interfaces can be changed and multiple implementations can be created and polymorphically substitutes for each other
  - assumes inheritance from abstract base classes or interfaces
  - the base class or interface is closed for modification but can be implemented differently and extended without modifying the parent
  - [Uncle Bob's article The Open-Closed Principle (1996)](https://drive.google.com/file/d/0BwhCYaYDn8EgN2M5MTkwM2EtNWFkZC00ZTI3LWFjZTUtNTFhZGZiYmUzODc1/view)
- A class or module should not have to be modified in order to integrate a new feature 
- One way to avoid having to modify a class to introduce a new feature is to implement the Single Responsibility Principle 
  - A class that does too many things may have to be modified; but a class that has **only one** responsibliity does not. It can simply be extended through inheritance to create new features without adding/modifying the base class