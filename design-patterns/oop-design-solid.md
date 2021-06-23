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
- [The Open-Closed Principle by Robert C. Martin (1996)](https://web.archive.org/web/20060822033314/http://www.objectmentor.com/resources/articles/ocp.pdf)
- [wikipedia article](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)
- [Maintainable Code and the Open-Closed Principle](https://severinperez.medium.com/maintainable-code-and-the-open-closed-principle-b088c737262)
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
- Example of the OCP being broken: 

```ts
class MonsterManager = {
  constructor(monsters, locations) {
    this.monsters = monsters
    this.locations = locations
  },
  
  private getRandomLocation () {
    function getRandomInt(max) {
      return Math.floor(Math.random() * Math.floor(max))
    }
    
    return this.locations[getRandomInt(this.locations.length)]
  },
  
  public rampageAll() {
    this.monsters.forEach(function(monster) {
      const location = this.getRandomLocation();
      
      if (Object.getPrototypeOf(monster) == Kaiju) {
        console.log(
          "The " + monster.type + " " + monster.name + 
          " is rampaging through " + location + "!"
        );
      } else if (Object.getPrototypeOf(monster) == GreatOldOne) {
        console.log(
          "The " + monster.type + " " + monster.name +
          " has awaken from its slumber in " + location + "!"
        )
      }
    })
  }
}

class Kaiju = {
  constructor(name) {
    this.name = name
    this.type = "Kaiju"
  }
}

class GreatOldOne = {
  constructor(name) {
    this.name = name
    this.type = "Great Old One"
  }
}

// Rampage!
const monsters = []
const locations = ["Athens", "Budapest", "New York", "Santiago", "Tokyo"]

const rodan = new Kaiju("Rodan", "Rodan")
monsters.push(rodan)

const gzxtyos = new GreatOldOne("Gzxtyos", "Great Old One")
monsters.push(gzxtyos)

const myMonsterManager = new Monstger)
myMonsterManager.init(monsters, locations)

myMonsterManager.rampageAll()
```

- This is bad form, because the `MonsterManager` class is not extensible. If you try to pass another monster type not hard-coded, it will break 
- Here is an example that leverages the OCP 

```ts 
abstract class Monster {
  name: string

  constructor(name: string) {
    this.name = name
  }

  abstract rampage: (location: string) => void

  getType() {
    return Object.getPrototypeOf(this)
  }
}

class MonsterManager = {
  constructor(monsters: Monster[], locations: string[]) {
    this.monsters = monsters
    this.locations = locations
  },
  
  private getRandomLocation () {
    function getRandomInt(max) {
      return Math.floor(Math.random() * Math.floor(max))
    }
    
    return this.locations[getRandomInt(this.locations.length)]
  },
  
  public rampageAll() {
    this.monsters.forEach((monster) => {
      const location = this.getRandomLocation()

      monster.rampage(location)
    })
  }
}

class KaijuMonster extends Monster {
  constructor(name: string) {
    super()
  }

  public rampage(location) {
    console.log(`The ${this.getType} monster ${this.name} rampages the city ${location}!`)
  }
}

const monsters = [
  new KaijuMonster("Valdomiro"),
]

const monsterManager = new MonsterManager(monsters, [
  "Athens", "Budapest", "New York", "Santiago", "Tokyo"
])
```

- Now as many monsters as you want to create can be extended from the `Monster` base class and the `MonsterManager` class doesn't care how a monster implements it's functions as long as it fulfils the requirements of it's type. `MonsterManager` is extensible because the `rampageAll` method can accept any kind of monster as long as it is a `Monster` type. There is loose coupling between monsters and the monster manager 
- Monsters are also extensible because they all inherit from a base type and can implement any amount of functionality they want in their own way and still be consumable by any downstream object that accepts a `Monster` type
- In summary, the OCP makes code more maintainable by allowing additional features and functionality to be added in such a way that does not break older parts of the code base. This is primarily done through abstractions. When an object has another object as a dependency, the dependency should be in charge of implementing it's algorithms or functionality and simply fulfil some kind of contract, such as an interface. Objects should thus always rely on abstractions rather than concretions. An object should only care what it's dependencies implement the correct properties and methods, and not ***how*** they implement them. Thus, when an object expects to interact with dependencies, those dependencies should either implement an interface or inherit from an abstract base class that guarantees the presence of required data and/or methods


## Liskov Substitution Principle
- [SOLID Class Design: The Liskov Substitution Principle](https://www.tomdalling.com/blog/software-design/solid-class-design-the-liskov-substitution-principle/)
- [The Circle Ellipse Problem](https://en.wikipedia.org/wiki/Circle%E2%80%93ellipse_problem)
- A type T should always be replaceable by any subtype S of T without altering any of the desirable properties of the program
- E.g. given a supertype `User` and a subtype `Admin`, each instance of `User` should be replaceable with `Admin` in a program without any negative results
- Barbara Liskov intended this as a general principle that does not always apply 
- For instance, abstract classes have no instances, so if all subclasses inherit from a base abstract class, the rule is meaningless
- Liskov refers to the LSP as behavioural subtyping: the behaviour of a supertype should be fully replaceable by the behaviour of a subtype
- LSP means, generally, the following holds for super and subtypes:
  - method parameters must be contravariant (cannot be different) 
  - return values must be covariant (meaning they change together i.e two related types have a method that sometimes returns a string, sometimes a number, but both always under the same conditions)
  - history constraint - methods on the subtype may not alter state that would not be permissible on the supertype
- If proper typings are used, for instance if you're using TypeScript or a statically-typed language, then it should be fairly straight-forward to not violate the LSP
- However, one way the principle may be violated is with a faulty inheritance hierarchy
- Consider the Circle-Ellipse problem, in which a circle cannot properly be a subtype of an ellipse, because an ellipse must have capabilities that would break it. Ellipses must be able to stretch their axes, but doing so will make the circle into a non-circle. Thus a circle can't implement the behavioural requirements of an ellipse. This same problem could be restated as the Bird-Penguin problem, in which a Penguin class cannot implement the behavioural requirements of a Bird without ceasing to be a Penguin. Consider this class: 

```ts 
abstract class Bird {
  abstract setLocation(longitude: number, latitude: number): void
  abstract setAltitude(altitutde: number = 0): void
  abstract draw(): void
}
```

- This abstract class will work great as a base class for many `Bird` objects, but what happens with you want to model a Penguin class?

```ts 
class Penguin extends Bird {
  // setAltitude becomes meaningless, since Penguins can't fly
}
```

- When a subclass inherits from a superclass, but one or more of the methods/properties become meaningless, irrelevant, or cause the subtype to cease to be what is is, like in the case of `Penguin` extending `Bird`, it's a violation of the Liskov Substitution Principle because `Penguin` couldn't be substituted for `Bird` (ignoring the fact that `Bird` is abstract - in this example, even if `Bird` could be instantiated, `Penguin` would still not be a correct behavioural subtype of `Bird`)
- Any time a method or property on a subtype can't be used or in some way is irrelevant or requires the supertype to be modified in some way to be functional, or if it throws an exception that is not thrown by the same method on the supertype, then you probably have a violation of the LSP 
- Stated differently, if a supertype has some functionality that, when applied to a subtype, would force the subtype not to be what it's supposed to be, it's a violation of the LSP. In this case, a Penguin forced to implement a flying capability would no longer be a Penguin
- Being careful about inheritance hierarchies can help prevent this problem. For instance, the problem in the example above could be fixed like this: 

```ts 
interface IFlyer {
  fly(): void
}

interface IGroundDweller {
  move(): void
}

abstract class Bird {
  abstract setLocation(longitude: number, latitude: number): void
  draw(): void
}

class Bluejay extends Bird Implements IFlyer {
  public fly(): void {
    // do fly logic
  }
}

class Penguin extends Bird Implements IGroundDweller {
  public move(): void {
    // do move logic
  }
}
```

- or the problem could be solved this way: 

```ts 
abstract class Bird {
  abstract setLocation(longitude: number, latitude: number): void
  draw(): void
}

class FlyingBird extends Bird {
  fly(): void {}
}

class GroundDwellingBird extends Bird {
  waddle(): void {}
}

```

- In either case, the inheritance hierarchy is more complex than the first example, but the added complexity is needed to fix the inheritance hierarchy so that any type of bird can be created that can behaviourally replace the base class 
- `FlyingBird` and `GroundDwellingBird` are both capable of behaviourally replacing `Bird` (again ignoring that it's an abstract class) because each subtype contains everything in the supertype plus additional functionality 
- It is generally a good idea to create slim base classes and allow individual subtypes to specify the functionality they need 
- This is similar to the OCP, in which it is best to allow dependency objects to implement their functionality indivdually, and write your client objects to rely on abstractions like an interface
- Stated more generally, in object-oriented design many problems can be solved by delegating functionality to appropriate objects. Each object should have a single responsibility that it implements a solution for, and client classes should call dependency methods 


## Interface Segregation Principle
- [The Interface Segregation Principle, Robert Martin (1996)](https://drive.google.com/file/d/0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi/view?resourcekey=0-3H2Ld4l-dIZZVRmHSpNLcA)
- No client object should be required to depend on methods it doesn't use 
- Stated another way, when a class implements an interface, the class should be written such that it actually needs and uses every property and method specified on the interface 
- If a class only needs a portion of the functionality on the interface, it's a sign that the interface is too fat and it's functionality should be segregated into multiple interfaces
- Therefore, functionality should be split up into specific thin interfaces called ***role interfaces*** 
- This leads to lower coupling of systems and easier refactoring
- When not working with interfaces and only classes, such as in JavaScript, classes should be broken up into smaller role classes and objects should be composed from role objects rather than inheriting from a fat class
- Objects can be composed of smaller role objects by adding role objects to a client object's prototype 
- E.g: 

```js 
class Character {
  constructor(name) {
    this._name = name
  }
}

class Mover {
  move(name) {
    console.log(`${name} moved`)
  }
}

class Attacker {
  attack(target) {
    console.log(`${target} has taken damage`)
  }
}

class Michael extends Person {
  constructor(name) {
    super(name)
  }
}

Object.assign(Michael.prototype, new Mover())
Object.assign(Michael.prototype, new Attacker())
```

- The above example demonstrates composition as a way to combine slim modular classes into larger modules as needed while keeping implementation separate and therefore loosely coupled and easily maintained
- In the composition example, different objects could be composed with only the modules they need rather than inheriting from a bloated class containing methods they don't need or implementing a large interface
- A major benefit of composing objects from smaller role objects, or using small role interfaces, is that they make your code less likely to break the Liskov Substitution Principle because smaller interfaces (or objects) are easy to fully implement


## Dependency Inversion Principle 
- High level modules (client objects) should not depend on low level modules (dependencies)
- Both high and low level modules should depend on abstractions 
- [The Dependency Inversion Principle, Robert C. Martin](https://drive.google.com/file/d/0BwhCYaYDn8EgMjdlMWIzNGUtZTQ0NC00ZjQ5LTkwYzQtZjRhMDRlNTQ3ZGMz/view?resourcekey=0-jRJy8Mi4CltX8KX84BqLFQ)
- [Dependency inversion principle Wikipedia](https://en.wikipedia.org/wiki/Dependency_inversion_principle)
- [SOLID Design Principles Explained: Dependency Inversion Principle with Code Examples](https://stackify.com/dependency-inversion-principle/)
- [SOLID Principles: Dependency Inversion Principle](https://dzone.com/articles/solid-principles-dependency-inversion-principle)
- [Very helpful YouTube video](https://www.youtube.com/watch?v=NnZZMkwI6KI&ab_channel=IAmTimCoreyIAmTimCorey)
- [Another helpful YouTube video](https://www.youtube.com/watch?v=S9awxA1wNNY)
- [A helpful video with vanilla JavaScript examples](https://www.youtube.com/watch?v=9oHY5TllWaU&ab_channel=WebDevSimplifiedWebDevSimplifiedOfficialArtistChannel)
- [JavaScript dependency injection in Node.js – friend or foe?](https://tsh.io/blog/dependency-injection-in-node-js/)

- Here's an example of conventional layered architecture: 

```ts
import { UserService } from "./User.service"

// high level module
export class UserController {
  private service

  constructor() {
    this.service = new UserService()
  }

  @Get("/:id")
  public async getUser(req, res, next) {
    const user = await this.service.findOne(req.params.id)
    return res.json(user)
  }
}

-----------------------------------------------------------

import { User } from "./User.model"

// lower level module
export class UserService {
  private model

  constructor() {
    this.model = User
  }

  public async findOne(id) {
    return await model.findOne(id)
  }
}
```

- In the example, UserController depends directly on the UserService module. Even though this works well and feels natural, it violates the Dependency Inversion principle
- This is bad because it means that UserController and UserService are tightly coupled, making UserController hard to reuse and easily broken if UserService changes 
- Instead, UserController should depend on an interface (abstraction) and any module that satisfies the interface contract that UserController relies on can be passed into UserController as the service 
- The `new` keyword should only be used in one place, usually at the very top level of the application. Instantiating concretions in a high level module breaks the DI principle because even when using interfaces, it still couples your high level module to a concrete instance of a low level module.
-  In most cases, especially when using a framework (such as NestJS, Spring, Laravel, .NET) the framework will automatically inject dependencies for you. If you are not using a framework, then at the top of your application you would have some kind of factory class that exposes methods that return instances of all your dependencies and injects them into client objects 
- Here is the above example refactored to satisfy the DI principle:

```ts 
export interface IDataStoreService {
  findOne(id: number): Promise<T>
}

------------------------------------------------------------------
import { IDataStoreService} from "./IDataStoreService.interface"

export class UserController {
  private service

  constructor(service: IDataStoreService) {
    this.service = service
  }

  @Get("/:id")
  public async getUser(req, res, next) {
    const user = await this.service.findOne(req.params.id)
    return res.json(user)
  }
}

------------------------------------------------------------------
import { IDataStoreService} from "./IDataStoreService.interface"

export class UserService implements IDataStoreService {
  private model 

  constructor(model: User) {
    this.model = model
  }

  public async findOne(id) {
    return await model.findOne(id)
  }
}

```

- Notice in the above example that you cannot find the `new` keyword anywhere. The example does not contain it, but as stated above, at the top level of the application these classes would be instantiated and the `UserController` constructor would be passed an instance of it's dependency by some kind of injector module 
- This means that `UserService` is decoupled from `UserController` since the latter does not reply directly on an instance of the former, but will be passed some kind of module by a factory that satisfies the `IDataStoreService` interface, and it doesn't matter what module that is, as long as it satisfied the interface contract
- This is also not the only way of implementing the DI Principle, but the implementation here is the most elegant for a TypeScript server application
- The Dependency Injection design pattern is one way to achieve dependency inversion, but it is not the only way to achieve it
- [A quick intro to Dependency Injection: what it is, and when to use it](https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/)