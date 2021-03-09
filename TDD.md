# Test-Driven Development
- Running automated testing reduces time and costs and improves accuracy 
- Application code is generally referred to as *implementation code* where as tests are referred to as *test code* 
- A set of tests for an application is referred to as a **test suite** 
- A test suite is generally run with `npm test`
- Test code is structured similar to implementation code and tests change as application code changes 
- Tests can serve as documentation for an application since tests contain a plain-text description of the functionality, as well as code to test that functionality: 

```js 
it('accepts the customer name', () => {
  const name = 'Hungry Person';
 
  browser.url('/');
  browser.setValue('#name', name);
  browser.click('#submit-order');
  browser.url('/');
 
  assert.include(browser.getText('#deliver-to'), name);
})
```
- Regression: when a new feature causes breakage of an older feature. When a previously developed feature no longer passes tests because of a new feature, the *functionality regressed*

## TDD Cycle 
1. Write a test for the desired feature, run the test. If written propertly, it should fail because you haven't written the feature yet.
2. Write the feature as fast as possible to satisfy the test. Code quality is not a big concern, only that the feature satisfies the test. 
3. Run the test. It should pass 
4. Refactor the code to remove duplication and increase efficiency, without changing behaviour. Run tests to make sure still passing 
5. Repeat

## Mocha
- Mocha is a test framework for node.js and for client-side JavaScript
- Install with `npm install mocha --save-dev`
- In your `package.json` file add a test script in scripts set to mocha: `"test": "mocha"`
- You can now run the test suite with `npm test`

### `describe` and `it` blocks 
- tests are grouped by `describe` functions, and define tests with `it` functions
- the test suite can be organized into a nested hierarchy that reflects the structure of the implementation code
- tests can provide informative messages with human-readable strings
- Example testing the `Math` native object in JavaScript: 

```js 
describe('Math', () => {
  describe('.max', () => {
    it('returns the argument with the highest value', () => {
      // Your test goes here
    })
    it('returns -Infinity when no arguments are provided', () => {
      // Your test goes here
    })
  })
})
```

- both `it` and `describe` accept a descriptive string and a callback as arguments
- inside `it` blocks, use the `assert.ok` method provided by node 
  - `assert.ok` allows you to compare values and throw errors as needed using a single function call
  - `assert` is a normal node module and can be imported using `const assert = require("assert")`
  - call like this: `assert.ok(1 + 2 === 3)`
  - if the expression passed to `assert.ok` evaluates to boolean `false`, an `AssertionError` is thrown
  - this tells mocha the test failed and mocha will log an error message to the console