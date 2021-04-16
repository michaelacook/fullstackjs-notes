# YAGNI, KISS, and DRY 
This set of notes will cover the foundational programming concepts of YAGNI, KISS and DRY.
[An overview of all three concepts](https://www.itexico.com/blog/software-development-kiss-yagni-dry-3-principles-to-simplify-your-life)

## YAGNI (You Ain't Gonna Need It)
- This principle states that you should aim to add the minimum set of features necessary to have a minimum viable product and not attempt to add extra features.
- You should only add a feature when you need it, never because you think you foresee a time in the future when you might need it 
- This is a principle of [Extreme Programming](https://en.wikipedia.org/wiki/Extreme_programming)
- Application features should be added incrementally, as needed
- Features built to try to cover some potential future need are more likely to become a drain on cost, effort and even get in the way of other features
- In general, in an agile environment requirements can shift. It's more efficient to build features as needed, instead of preemptively building them only to have requirements shift later, making a feature useless or in need of changes
- YAGNI doesn't apply to spending efforts on refactoring to make your code more modular or easy to modify in the future 
  - YAGNI will actually become a burden if it doesn't allow you to spend extra time making your code easily modifiable
  - You will need to add features in the future, when you need them, and you need to be able to do that without too much trouble 
  - Therefore, there is nothing contrary to YAGNI about making the decision to refactor and make code malleable. You WILL need your code to be malleable
  - The whole point is evolutionary, modular design that responds to changing needs and requirements
- [Great article about YAGNI with in-depth examples](https://martinfowler.com/bliki/Yagni.html)

## KISS (Keep It Simple, Stupid)
- [A Detailed Explanation of the KISS Principle in Software](https://thevaluable.dev/kiss-principle-explained/)
- Design should always be as simple as possible
- To change software, you have to understand how it works. To understand how it works, you have to have a mental model or representation of it in your mind. Complex software is difficult to form a mental representative of 
- Therefore, keep software simple, so it can be easily updated, maintained and modified
- Complex systems create unhappy developers who risk burnout, as well as frustrated clients 
- You can't always avoid complexity, as complexity will largely reflect complex business requirements. However, when complexity is required it must be properly managed. Therefore, **make requirements simpler**. Collaborate with project managers and stakeholders to reduce the complexity of the business requirements as much as possible 
- Have less code. Good code is deleted code
- Remove dead code. If it's no longer doing anything but sitting there making you look smart, just delete it
- If you don't need it yet, don't write it. That's YAGNI. Adding unneeded features adds complexity

### Conditionals and Cyclomatic Complexity 
- A complex control flow with many conditionals or switch statements introduces complexity into the code 
- cyclomatic complexity is a metric of every path the execution of the program can take at runtime
- Complex control flow makes it difficult to form a mental model of software 
- Cyclomatic complexity is not always bad. A lot of if statements may still be quite readable. But it can be a hint. It's also possible (in JavaScript, at least) to substitute a lot of ifs with a simpler way of achieving the same thing. E.g 

```js 
// complex 

function validate(obj) {
  if (obj.title === undefined) {
    return false 
  }

  if (obj.date === undefined) {
    return false 
  }

  if (obj.author === undefined) {
    return false
  }

  if (obj.createdAt === undefined) {
    return false
  }

  if (obj.updatedAt === undefined) {
    return false
  }
}
```

```js 
// simpler 

function validate(obj) {
  for (let el of ["title", "date", "author", "createdAt", "updatedAt"]) {
    if (obj[el] === undefined) {
      return false
    }
  }
}
```

- notice how the second function accomplishes the same thing with only a single if statement instead and has far fewer lines of code. This is reduced complexity
- avoid nesting, especially nested conditionals. Some times data is complex and must be nested, but nested control flow may be a sign that things are getting needlessly complex
- Nesting is a sign of interconnectedness, which is complexity. avoid it, refactor if possible 
- Too much complexity in control flow starts to make it hard to know the outcome of execution at runtime
- Conditions in conditionals can get overly complex too. E.g: 

```js 
if (!(a == true && (b == false || !(z < c || c == true)) && !d)) {
 // do a thing
}
```

- this type of complexity could be reduced by using logical equivalencies from propositional logic '

### Simple Architecture 
- [almost] never use global states. Program design that depends on setting global states is bad bad bad
- avoid lasagna architecture: too many unneeded layers 
- Too many unneeded layers creates too many things that may need to change and a more complex mental model of the software. You will also have to write way more tests if you're writing tests diligently and trying to get good test coverage
- avoid unnecessary abstractions that are intended to make the software more flexible, but at the expense of simplicity. YAGNI again: don't spend time introducing complexity in the form of abstractions meant to make a future feature more easy. You ain't gonna need it

### Abstractions and Complexity 
- it's no good to make simple sounding functions that abstract away all kinds of hidden complexity
- write abstractions (functions, classes etc) that have a single purpose and have a simple procedure
- **self-containing logic** or ***encapsulation*** is how functions and classes should be written. They should depend on nothing outside of them. this is a fundamental principle of object-oriented design

### The Cost of Polymorphism
- classes that implement an interface can implement it in different ways, leading to different classes all implementing the same interface, and therefore being "the same" but actually being implemented very differently 
- This can introduce complexity: makes it difficult to know the implementation used at runtime, interfaces need to be constantly updated
- interfaces create flexibility and the ability to swap implementations easily, but introduce complexity. use them only when really needed

### The Dependency Problem 
- dependencies couple your software with other software. This introduces complexity  
- do not use a third party dependency if you don't really need it 
- always weigh the risk and complexity of using third party software against the benefits and reduction of code you will have to personally write 
- avoid creating lateral complexities between classes or modules and if you need to do so, ask if it is really necessary

### Don't Try to Outsmart Anyone 
- don't try to appear clever
- "Make simple tasks simple!" -Bjarne Stroustrup 
- you do not need to use every design pattern or invent new and smart ways of doing things that are easily accomplished more simply 
- "Everyone knows that debugging is twice as hard as writing a program in the first place. So if you're as clever as you can be when you write it, how will you ever debug it?" - Brian Kernighan
  
### Take Home 
- You are not stupid. Your code should be stupid-simple 
- It should be so simple that a junior developer can jump right in
