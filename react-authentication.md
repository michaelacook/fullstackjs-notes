# Basic Authentication in React
* This set of notes will only summarize the basic concepts for implementing Basic Authentication in a React application, not provide a detailed discussion
## Register an account 
* Create a component that renders a form for getting the necessary data from a user to create an account according to API requirements 
* Send those data to the correct API endpoint using your method of choice (axios, fetch API, etc)
* If you wish to have your user automatically authenticated after signing up, you can call a method to authenticate the user based on the provided credentials for signing up immediately after creating the user and before redirecting 
* This can be an improvement to user experience

## Login 
* It is a good idea to have a separate class that encapsulates methods for interacting with the API 
* When fetching the user for login purposes, the method for fetching the user should send an Authorization header containing the encoded username and password to the server
* An example of a method for interacting with the API: 

```js 
api(path, method = "GET", body = null, requiresAuth = false, credentials = null) {
    const url = config.apiBaseUrl + path

    const options = {
      method,
      headers: {
        "Content-Type": "application/json; charset=utf-8",
      },
    }

    if (body !== null) {
      options.body = JSON.stringify(body)
    }

    if (requiresAuth) {
      const encodedCredentials = btoa(`${credentials.username}:${credentials.password}`)
      options.headers['Authorization'] = `Basic ${encodedCredentials}`
    }
    return fetch(url, options)
}
```
* The `api` method accepts a number of arguments that specify how it should make a request to the server, then uses the `fetch` api to carry out the request 
* one argument specifies whether the request requires authentication with `requiresAuth`. If the request (such as fetching a user for login) requires authentication, then username and password will be passed as an object `credentials`
* The username and password are base-64 encoded first using the `btoa()` function
* `api` will then send an Authorization header to the server 
* The API will require the Authorization header be sent for certain protected routes. You will need your client-side application to send this header when interacting with the API to access any protected routes 
* Your method to fetch a user might look like this: 

```js 
async getUser(username, password) {
    const response = await this.api(`/users`, "GET", null, true, { username, password })
    if (response.status === 200) {
        return response.json().then((data) => data)
    } else if (response.status === 401) {
        return null
    } else {
        throw new Error()
    }
}
```

* If fetching the user is successful then you can update application state to hold the user's credentials (but not password) and set a cookie to persist the user across page reloads. This may look like this: 

```js 
signIn = async (username, password) => {
    const user = await this.data.getUser(username, password)
    if (user !== null) {
        this.setState({
            authenticatedUser: user
        })
        Cookies.set('authenticatedUser', JSON.stringify(user), {
            expires: 1,
        })
    }
    return user
}
```

* The [js-cookie](https://github.com/js-cookie/js-cookie) package allows you to set cookies on the client side 
* Your application state should then be set up to set the authenticated user either from a set cookie or null if no cookie exists: 

```js 
state = {
    authenticatedUser: Cookies.getJSON('authenticatedUser') || null
}
```

## Signout 
* When a user wants to sign out, you will have them click a link navigating to a route (probably "/signout") that renders a component that signs the user out and redirects to another route 
* The signout method on the class interacting with your data store should set application state for the authenticated user to null, and should clear the cookie associated with the authenticated user before redirecting 

```js
signOut = () => {
  this.setState({ authenticatedUser: null }) 
  Cookies.remove('authenticatedUser')
} 
```
* You can then call your signout method in the `useEffect` hook in your component:

```js 
import React, { useEffect } from "react"
import { Redirect } from "react-router-dom"

export default ({ context }) => {
  useEffect(() => context.actions.signOut())
  return <Redirect to="/" />
}
```

## Protected routes 
* You will have routes in your application that you only want authenticated users to have access to, otherwise be redirected somewhere else 
* This can be accomplished with a higher order component that conditionally redirects to the protected path if the user is authenticated or redirects to a signup page otherwise 
* For instance:  

```js 
import React from "react"
import { Route, Redirect } from "react-router-dom"
import { Consumer } from "./Context"

export default ({ component: Component, ...rest }) => {
  return (
    <Consumer>
    {context => (
      <Route
        {...rest}
        render={props => context.authenticatedUser ? (
            <Component {...props} />
          ) : (
            <Redirect to={{
              // redirect unauthenticated user to /signin
              pathname: '/signin',
              /*
              The state property holds information about the user's current location 
              (i.e., the current browser URL). That way, if authentication is successful, 
              the router can redirect the user back to the original location 
              (from: props.location).
               */
              state: { from: props.location },
            }} />
          )
        }
      />
  )}
  </Consumer>
  ) 
}
```
* This component returns a `Route` component that accepts any number of props through a rest parameter as well as a `Component` to render
* If application state holds a currently authenticated user, the returned `Route` renders the component passed to it, otherwise it returns a `Redirect` component 
    * The `Redirect` passes an object to it's `to` prop that contains `pathname` and `state` properties 
    * The `pathname` is the location to redirect to 
    * The `state` tells the `SignIn` component the location the redirect is coming from, so that it can redirect back to that previous location after authenticating the user
    * Redirecting a user back to the path they were trying to access before authenticating improves user experience 
* Any routes you want to be protected and require the user be authenticated you can render inside your `Switch` component with your `PrivateRoute` component instead of a `Route` component: 

```js 
<Router>
    <div>
      <HeaderWithContext />

      <Switch>
        <Route exact path="/" component={Public} />
        <PrivateRoute path="/authenticated" component={AuthWithContext} />
        <PrivateRoute path="/settings" component={AuthWithContext} />
        <Route path="/signin" component={UserSignInWithContext} />
        <Route path="/signup" component={UserSignUpWithContext} />
        <Route path="/signout" component={UserSignOutWithContext} />
        <Route component={NotFound} />
      </Switch>
    </div>
  </Router>
```

## Redirect back to previous path 
* As mentioned in the previous section, it is a good practice to have your application redirect a user back to the previous path they were trying to access before being required to authenticate 
* Your component for handling protected routes should perform a redirect with the `to` prop set to the following object: 

```js 
{
    // redirect unauthenticated user to /signin
    pathname: '/signin',
    // set the location they are being redirected from 
    state: { from: props.location }
}
```
* Your login component should then redirect the user back to the previous location: 

```js 
submit = () => {
const { context, history } = this.props

// get previous location if it exists, else set it to some default
const { from } = this.props.location.state || { from: { pathname: '/authenticated' }}
const { username, password } = this.state
context.actions.signIn(username, password)
    .then(user => {
    if (user === null) {
        this.setState(() => {
        return { errors: [ 'Sign-in was unsuccessful' ] }
        })
    } else {
        // once login is complete, redirect to the location the user came from
        history.push(from)
    }
    })
    .catch(err => {
    history.push('/error')
    console.log(err)
    })
}
```