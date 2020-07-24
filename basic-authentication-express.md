# REST API Authentication with Express

## Basic Authentication
* Key-based authentication is a common approach to authenticating users 
* involves a unique key or identifier: usually username or email and a password
* [HTTP authentication](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)
* The Basic Authentication scheme is part of the HTTP authentication framework
* When using Basic Authentication the client sets an Authorization HTTP header on each request that is sent to a protected route on the server
* Example of an Authorization header and value: 

    ```
    Authorization: Basic am9lQHNtaXRoLmNvbTpqb2VwYXNzd29yZA==
    ```

* The header fieldname is "Authorization" followed by a colon and then the value after
* The string of random letters and numbers is a base64 token generated out of the username and password in the following format: `username:password`
* To convert the username and password to a token use the `btoa` function: 

    ```js
    const authToken = btoa(`${username}:${password}`) 
    ```
* The [`basic-auth`](https://www.npmjs.com/package/basic-auth) package is good for parsing the Authorization header


### Hashing Passwords 
* A good package for hashing passwords is [`bcrypt`](https://www.npmjs.com/package/bcryptjs)
* Passwords should never be stored raw, they should always be hashed

### Authentication Middleware
* In Express one of the best ways to implement authentication is to write a middleware module to run on protected routes
* The middleware should:
    * parse the user's credentials from the Authorization header
    * attempt to retrieve the user from the data store by their username or other unique identifier
    * if retrieved, compare compare passwords
        * if a match, then store the user on the `request` object and call `next()`
        * if no match, return a `response` with a `401 Unauthorized` status
* Using the `basic-auth` package is a better idea however

    ```js 
    const auth = require('basic-auth')

    const authenticateUser = (req, res, next) => {
        let message = null
        const credentials = auth(req)

        if (credentials) {
            const user = users.find(u => u.username === credentials.name)

            if (user) {
                const authenticated = bcryptjs.compareSync(credentials.pass, user.password)

                if (authenticated) {
                    req.currentUser = user
                } else {
                    message = `Authentication failed for username: ${user.username}`
                }
            } else {
                    message = `Authentication failed for username: ${credentials.username}`
                }
        } else {
            message = 'Auth header not found'
        }

        if (message) {
            console.warn(message)
            res.status(401).json({
                message: "Access Denied"
            })
        }
    }
    ```
* It is a good idea to give a vague message when access is denied because a more specific message could inadvertently help a bad actor
* You can now add this middleware to a route you want protected by adding it as a callback to the route handler
* You can access the user in the route handler callback with `req.user` if authentication was successful

### Adding a Basic Authentication header in Postman
* The Basic Authentication header contains a username and a password
* packages like [`axios`](https://github.com/axios/axios) or the fetch API allow you to send request headers from the client to the server 
* To send a request header in Postman, switch to the "Authorization" tab and select "Basic Auth" for "TYPE"
* provide the username and password you want
