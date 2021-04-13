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
  - A good test has at least three parts: the setup, exercise, and verification
    - Setup: create objects, variables and set conditions the test depends on. This is good for readability and expressiveness 
  - Exercise: execute the functionality you are testing
  - Verify: check your expectations against the result of the exercise phase. Use `assert` here
  - E.g: 

```js 
// 3 phase approach
describe('.pop', () => {
  it('returns the last element in the array [3phase]', () => {
    // Setup
    const knightString = 'knight';
    const jediPath = ['padawan', knightString];

    // Exercise
    const popped = jediPath.pop();

    // Verify
    assert.ok(popped === knightString);
  })
})
```

- Some tests require a fourth step, which is the teardown
  - some tests make changes to their environment that will affect other tests, and therefore need to be reversed at the end. For instance, manipulating files and directories, file permissions, database records, etc 
  - The teardown makes the test isolated 
  - E.g: 

```js 
const assert = require('assert');
const fs = require('fs')

describe('appendFileSync', () => {
  const path = './message.txt'
  
  it('writes a string to text file at given path name', () => {

    // Setup
    const str = 'Hello Node.js'
    
    // Exercise: write to file
    fs.appendFileSync(path, str)

    // Verify: compare file contents to string
    const contents = fs.readileSync(path)
    assert.ok(contents.toString() === str)

    // Teardown: delete path
    fs.unlinkSync(path);

  })
})
```

- The above test creates a new file with the text "Hello Node.js", but running the test twice will produce a fail because the test will append the string to the file again, changing it's contents. So at the end of the test, the file is deleted
- This is not always reliable though. An error could occur in the test before the teardown is reached. Mocha provides a number of hooks, one of which allows you to run a teardown after each `it` execution. E.g: 

```js
const assert = require('assert');
const fs = require('fs')

describe('appendFileSync', () => {
  const path = './message.txt'

  afterEach(() => {
    // Teardown: delete path
    fs.unlinkSync(path);
  })
  
  it('writes a string to text file at given path name', () => {

    // Setup
    const str = 'Hello Node.js'
    
    // Exercise: write to file
    fs.appendFileSync(path, str)

    // Verify: compare file contents to string
    const contents = fs.readileSync(path)
    assert.ok(contents.toString() === str)
  })
})

```

- Using the `afterEach` hook ensures that even if some error occurs, the teardown will still be executed

### `assert`
- Using `assert.ok` is fine for many tests
- `assert` has other methods you can use though that will make tests more expressive and therefore document your implementation code better
- Use `assert.equal` to test equality
- To do a strict equality check, use `assert.strictEqual` since the former uses loose equality `==` and the latter uses strict equality `===`
- Use `assert.deepEqual` to check if two objects are the same. the other two equality tests will both throw an error because two objects, even if they have all the same keys and values, are not generally considered to be the same (even though they are for all intents and purposes)
  - `assert.deepEqual` will use loose equality to test all keys and values in both objects
  - this also works for arrays, since arrays are actually just objects with numbered indices as keys  
- The `assert` node module contains more methods as well: [docs](https://nodejs.org/api/assert.html)

### (silly) Example of a test suite 

-`index.js`: 

```js 
// Define a rooster
Rooster = {}

// Return a morning rooster call
Rooster.announceDawn = () => {
  return 'moo!'
}

// Return hour as string
// Throws Error if hour is not between 0 and 23 inclusive
Rooster.timeAtDawn = (hour) => {
  if (hour < 0 || hour > 23) {
    throw new RangeError;
  } else {
    return hour.toString();
  }
}

module.exports = Rooster;
```

- `index.test.js`: 

```js 
const assert = require("assert")
const Rooster = require("../index.js")

describe("Rooster", () => {
  describe(".announceDawn", () => {
    it("returns a rooster call", () => {
      const expected = "cock-a-doodle-doo!"
      const actual = Rooster.announceDawn()
      assert.equal(actual, expected)
    })
  })

  describe(".timeAtDawn", () => {
    it("returns its argument as a string", () => {
      const expected = "string"
      const actual = typeof Rooster.timeAtDawn(5)
      assert.strictEqual(actual, expected)
    })

    it("throws an error if passed a number less than 0", () => {
      assert.throws(Rooster.timeAtDawn(-1))
    })

    it("throws an error if passed a number greater than 23", () => {
      assert.throws(Rooster.timeAtDawn(24))
    })
  })
})
```

## Server Testing
- server tests only test the HTTP response
- tests define expected behaviour of interactions and check actual responses against those expectations
- commonly used to test APIs but can also be used to test responses for any backend
- can also be used to test status codes and error messages
- A common set of libraries for testing node servers are Chai, jsdom and SuperTest

### Testing HTML Reponses
- the jsdom library can be used to test HTML output from a server
- [JSDOM: A Guide to How to Ge Started and What You Can Do](https://www.testim.io/blog/jsdom-a-guide-to-how-to-get-started-and-what-you-can-do/)
- [jsdom docs](https://github.com/jsdom/jsdom)

### SuperTest
- used to test server responses 
- [docs](https://www.npmjs.com/package/supertest)
- `npm install supertest --save-dev`
- To set up, pass the `app` instance of Express into the `request` object: 

```js 
const express = require("express")
const app = express()

request(app)
  .get('/user')
  .expect('Content-Type', /json/)
  .expect('Content-Length', '15')
  .expect(200)
  .end(function(err, res) {
    if (err) throw err
  })
```

An example with mocha: 

```js 
describe('GET /user', () => {
  it('responds with json', (done) => {
    request(app)
      .get('/user')
      .set('Accept', 'application/json')
      .expect('Content-Type', /json/)
      .expect(200, done);
  })
})
```

### Server Testing Patterns
- it can be faster to test the server instead of writing feature tests that test the client 
- you can test HTML output from the server after sending a test request using jsdom and supertest
- server testing should always test status codes as this provides the most basic confidence that the server is responding properly 
- e.g ```js assert.equal(response.status, 200);```
- testing that the server produces the intended HTML (or JSON) output given specific url parameters, query parameters, HTTP headers, etc is also important
  - when designing tests it is important to consider both intended and unintended behaviour