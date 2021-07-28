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