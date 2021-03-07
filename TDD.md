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