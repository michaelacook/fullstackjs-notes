# Common Design Patterns 
This set of notes details some of the fundamental design patterns every programmer should know. Most examples are in TypeScript.

## What are design patterns?
- [What's a design pattern?](https://refactoring.guru/design-patterns/what-is-pattern)
- Design patterns are time-tested concepts that can be applied in programming to solve a design problem 
- They are like blueprints that can be implemented in your code, but not specific pieces of code or dependencies you can import into your codebase
- Patterns are like algorithms in that they both describe typical solutions to common problems. However, they are different because while algorithms always describe a specific set of actions to achieve an end goal, a design pattern is a high-level description of a solution without an implementation. An algorithm will look very similar across different programs and languages, but a pattern may be implemented very differently depending on circumstances 
- Descriptions of design patterns usually consist of: 
  - 1. Intent of the pattern. What the typical problem is and what the solution is
  - 2. Motivation further explains the problem and the solution the pattern makes 
  - 3. Structure of classes shows each part of the pattern and how they are related 
  - 4. A code example in a popular programming language to illustrate
- [JavaScript design patterns](https://www.dofactory.com/javascript/design-patterns)

## Seven most important patterns 
-[The 7 Most Important Software Design Patterns](https://medium.com/educative/the-7-most-important-software-design-patterns-d60e546afb0e)


## Singleton
- [Singleton](https://refactoring.guru/design-patterns/singleton)
- A creational pattern
- Use when you need one and only one instance of a class globally available throughout the application 
- E.g. a database connection or a web sockets connection
- You make the constructor private, a private instance field, and a static method that sets the private instance field with an instance
- Whenever the static instance method is called, the cached instance is returned. So only one instance is ever created 
- [Singleton in TypeScript](https://refactoring.guru/design-patterns/singleton/typescript/example)
- [How To Work With Singletons in JavaScript](https://www.digitalocean.com/community/tutorials/js-js-singletons)
- This pattern is useful, but also breaks the Single Responsibility Pattern because it solves two problems at once: it limits the creation of new instances, and it makes a cached instance globally available 
- Example: 

```ts 
export class Singleton {
  private static instance: Singleton = null;

  private constructor() {}

  public static getInstance() {
    if (Singleton.instance === null) {
      Singleton.instance = new Singleton()
    }

    return Singleton.instance
  }

  public doBusinessLogic(args) {
    // do stuff
  }
}
```

- The Singleton pattern cannot be implemented the same way in JavaScript, because JavaScript does not allow for private constructors: 

```js 

export class Singleton {
  static #instance = null;

  #constructor() {}

  static getInstance() {
    if (Singleton.#instance === null) {
      Singleton.#instance = new Singleton()
    }

    return Singleton.#instance
  }
}

// Uncaught SyntaxError: Class constructor may not be a private method
```

- Instead, in JavaScript the constructor must be deleted after the instance is created: 

```js 
export const SingletonFactory = (function(){
  function SingletonClass() {
    // ...
  }

  let instance;

  return {
    getInstance: function(){
      if (!instance) {
        instance = new SingletonClass()
        delete instance.constructor
      }
      return instance
    }
  }
})()
```

- Another way to achieve this is with the `Object.freeze` method: 

```js 
class Singleton {
  // 
}

const SingletonInstance = new Singleton()
Object.freeze(SingletonInstance)

export default SingletonInstance
```

## Builder
- [Builder](https://refactoring.guru/design-patterns/builder)
- [helpful YouTube video](https://www.youtube.com/watch?v=M7Xi1yO_s8E&ab_channel=WebDevSimplified)
- [JavaScript Builder](https://www.dofactory.com/javascript/design-patterns/builder)
- A creational pattern
- For constructing complex objects step by step
- The problem: sometimes you need really complex objects that are hard to construct 
  - Imagine an object that represents a house. Houses are very complex buildings: they have many rooms and parts, electrical systems, heating and cooling systems, some have pools, some have other outdoor features, etc 
  - To create objects to represent all the different variations of types and features you would need a lot of subclasses that extend a base class `House` 
  - Otherwise, you would need one class with a massive unwieldy constructor with a lot of parameters 
  - Neither of these scenarios is ideal 
- The solution: the Builder pattern in which you extract the code for creating the object into separate objects called ***builders***
  - Builders create individual components of the complex object, and not all builders need to be called each time 
  - the building of the object is broken up so that you can call the builders you need for the particular object you're creating 
  - Because you may have many different builders that create different variations of an object, you can also have classes called Directors that define the order in which to call specific construction routines that can be reused, although this is not always necessary and often builders can be called directly by the client class
- Use the Builder pattern when construction of various representations of an object involves similar steps that differ only in the details
- Use the Builder pattern when you have a large and complex object with a lot of properties
- You can also use the Builder pattern when you want to create many different representations of an object that have variations or need different ordered steps and configurations
- This set of notes does not go into much detail. Consult the article for a more detailed discussion of how to implement 

## Factory Method 
- [Factory Method wikipedia entry](https://en.wikipedia.org/wiki/Factory_method_pattern)
- [Factory Method Refactoring Guru](https://refactoring.guru/design-patterns/factory-method)
- [Factory Method dofactory](https://www.dofactory.com/javascript/design-patterns/factory-method)
- [Factory Method in Python - useful for any language](https://realpython.com/factory-method-python/)
- [Helpful YouTube tutorial](https://www.youtube.com/watch?v=ub0DXaeV6hA&ab_channel=DerekBanas)
- [Another helpful YouTube tutorial](https://www.youtube.com/watch?v=xN7EFHU_rXA&ab_channel=RawCoding)
- Also known as the Virtual Constructor pattern
- Creational pattern 
- For dynamically calling constructors when you don't know which class constructor you will need to call until runtime
- Consider a situation in which you have a single base class or an interface and a number of derived classes. Derived classes need to be created dynamically at runtime. The Factory Method pattern abstracts away object creation to a factory method on the base class that handles which class to dynamically instantiate at runtime
- For instance, a cross-platform UI library that works on web, mobile and desktop. A dialog box needs buttons, but buttons are created very differently on different platforms (i.e, HTML on web, XML mobile, etc)
- When a dialog box is created for a specific platform, it should automatically have the correct type of buttons on it. Instead of achieving this with a lot of conditionals, you can use a factory method: 

```js 
export interface IDialog {
  text: string
  open(): void 
  close(): void 
  render(): void
  createButton(): Button
}

export interface IButton {
  click(): void 
  render(text: string): void
}

export class WebButton implements IButton {
  public click(callback): void {
    callback()
  }

  public render(text: string): void {
    document.dialog.append(`<button>${text}</button>`)
  }
}

export class Dialog implements IDialog {
  public text: string;

  constructor (text: string) {
    this.text = text
  }

  abstract public createButton() {}

  public open(): void {}

  public close(): void {}

  public render(): void {}
}

// override the createButton method and return an HTML button
export class WebDialog extends Dialog {
  public createButton(): Button {
    return new WebButton()
  }
}
```

- Every dialog extending the base `Dialog` class will have to implement the `createButton` factory method, but each implementation will create the type of button appropriate to it's OS platform
- These notes are minimal. For more information, consult the material listed above

## Decorator Pattern
- [Decorator pattern - Wikipedia entry](https://en.wikipedia.org/wiki/Decorator_pattern)
- [Refactoring Guru - Decorator Pattern](https://refactoring.guru/design-patterns/decorator)
- [Helpful YouTube tutorial](https://www.youtube.com/watch?v=j40kRwSm4VE&ab_channel=DerekBanas)
- [Pizza example in Java](http://www.newthinktank.com/2012/09/decorator-design-pattern-tutorial/)
- A structural design pattern
- The Decorator pattern is a more flexible alternative to inheritance that allows you to add behaviour to an individual object rather than a class and therefore all objects instantiated from that class 
- It allows you to dynamically add functionality to an object at run-time
- Decorators are wrapper objects that can contain an instance of another object and give them extra functionality
- "Decorator is a structural design pattern that lets you attach new behaviors to objects by placing these objects inside special wrapper objects that contain the behaviors." - Refactoring Guru
- With decorators you separate functionality into layers, or separate classes with single responsibilities and then compose objects from a base object and decorators implementing a common interface
- Adding decorators to an object never causes a break when a client object consumes it, precisely because each decorator wrapping the base object implements a common interface, simply extending or overriding the base object's methods
- To implement the decorator pattern, you create an interface that will be implemented both by the class whose functionality you want to extend, as well as the base class for all decorator objects 
  - Create an abstract decorator class that implements the interface, and then extend the abstract decorator class for each concrete decorator, overriding it's methods to add the desired functionality 
- E.g


```js 
// All pizzas and all topping decorators must implement this interface
interface IPizza {
  getCost(): number 
  getDescription(): string
}

// a basic pizza that we will want to add toppings to
class BasicPizza implements IPizza {
  public getCost(): number {
    return 5
  }

  public getDescription() {
    "Thin crust and mozzarella pizza"
  }
}

// base decorator class
// all decorators adding individual behaviours will extend this class and override it's methods
abstract class ToppingDecorator implements IPizza {
  protected pizza

  constructor(pizza: IPizza) {
    this.pizza = pizza
  }

  public getDescription(): string {
    return this.pizza.getDescription()
  }

  public getCost(): number {
    return this.pizza.getCost()
  }
}

class Pepperoni extends ToppingDecorator {
  constructor(pizza: IPizza) {
    super()
  }

  public getDescription: string {
    return this.pizza.getDescription() + ", and pepperoni"
  }

  public getCost: number {
    return this.pizza.getCost() + 1.5
  }
}

// a pepperoni pizza composed from the basic pizza and a decorator 
const pepperoniPizza = new Pepperoni(new BasicPizza())
```

- the Decorator pattern allows functionality to be extended without the pitfalls of using inheritance 
- it preserves the Open/Closed principle by structuring your objects in such a way as to be extensible without needing to be internally modified 
- it preserves the Single Responsibility principle by delegating different functionalities to many separate classes
- multiple inheritance does not exist in most languages; decorators allow inheriting behaviour from multiple objects without needing inheritance
- use the decorator pattern when you need to dynamically add a behaviour to an object without modifying it's class or breaking client code 
- use the decorator pattern when you need to extend the behaviour of an object but inheritance is not feasible or creates additional problems, breaks client code, etc