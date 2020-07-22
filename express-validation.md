# REST API Validation with Express
* This set of notes covers setting up server-side data validation with Express.js
* Data validation is necessary to prevent bad actors from performing harmful actions on a site or web application
* Validation is also useful for letting the user know if they input data that can't be used by the application
* Validation can happen on the client side, server side, as well as at the level of the database, though this depends on which RDMS you are using. In a client-server application implementing validation at multiple levels is a good idea
* Client validation is quicker because it doesn't rely on an http request, but server validation can work as a check if a savvy user bypasses client validation; database validation is useful for preventing a third party application or script from persisting bad or malicious data in your database

## Data Validation Types 
* You can validate data for: 
    * Data type 
        * string, number, float, datetime, etc
    * Range and constraint validation
        * Make sure values fall within a certain range
        * i.e birthdays, string lengths, etc
    * Consistency check
        * Make sure provided values are logically consistent
        * i.e start date of a course before the end of the course, matching passwords

## `express-validator` 
* [`express-validator` documentation](https://express-validator.github.io/docs/)
* `express-validator` uses `validator.js`
Docs: [`validator`](https://github.com/validatorjs/validator.js) to get all validators
* run `npm install express-validator --save`
* require the module where you are going to use it: 

    ```js 
    const { check, validationResult } = require('express-validator');
    ```
* Create a validation chain for an item in the POST payload: 

    ```js 
    const nameValidationChain = check('name')
      .exists({ checkNull: true, checkFalsy: true })
      .withMessage('Please provide a value for "name"');
    ```
* Add the validator as the second argument to your route handler: 

    ```js
    router.post('/users', nameValidationChain, (req, res) => { ... }
    ```
* You will now have access to any error messages inside the route handler callback: 

    ```js 
    router.post('/users', nameValidationChain, (req, res) => {

        const errors = validationResult(req);

        if (!errors.isEmpty()) {
            const errorMessages = errors.array().map(err => err.msg);
            return res
              .status(400)
              .json({ errors: errorMessages });
        }

        ...
    }
    ```
* Error messages are accessible on an object via the `msg` property
* [A good stackoverflow post about implementing `express-validator` using separation of concerns](https://stackoverflow.com/questions/55772477/how-to-implement-validation-in-a-separate-file-using-express-validator)