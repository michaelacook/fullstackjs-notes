# React Router 
* React Router is a declarative, component based client-side routing solution for React 
* React Router allows you to create Single Page Applications (SPAs) in which navigation from section to section in the app is done without reloading the page
* [Documentation](https://reacttraining.com/react-router/web/guides/quick-start)

## Setup
* To get set up, first run `npm install react-router-dom --save`
* Import the library into the file where your main component lives

```js
import { BrowserRouter, Route, Switch } from "react-router-dom"
```
* You are now ready to start writing routes

## Routes
* React Router is component-based, so to declare routes you use several two different components: `BrowserRouter` and `Route`
* Inside the `return` statement of your main component, or inside the `return` statement of your `render` method, add the `BrowserRouter` component. This component wraps components and listens for changes to the url and tells `Route` components information about the current url 

```js 

const App = () => (
    <BrowserRouter>
    </BrowserRouter>
)
```

* Inside the `BrowserRouter` component you nest as many `Route` components as you need. These components specify a `path` prop and a `component` prop. When the url matches a `Route`'s specified path, it will render the component assigned to it

```js
import Home from './Home';

const App = () => (
    <BrowserRouter>
      <Route exact path="/" component={Home} />
    </BrowserRouter>
)
```

* The `exact` prop can be passed to a route so that it will render it's component **only** when the url exactly matches the specified path. Otherwise the route will match with any url that has the same first character (e.g "/" = "/asdf" = "/asdf/zxcv")

* You can also specify a `render` prop that accepts a callback and renders a component instead of a `component` prop
* This is useful when you want to pass props to the component being rendered, which you cannot do when you are passing a reference to the desired component with `component`

```js
const App = () => (
    <BrowserRouter>
      <Route exact path="/" render={() => <Home user={this.state.user} />} />
    </BrowserRouter>
)
```

### Creating a 404 page
* You will want your users to be see some kind of user-friendly error message when they navigate to a route where no component is specified 
* To do this you need to first, create a 404 component that looks and behaves as you want it to and import it into your main component file 
* Next, you need the `Switch` component, which wraps all your `Route` components 
* `Switch` makes it so that the first specified route to match the current url will render it's component, allowing you to specify a 404 component at the bottom of your routes that will display when no other route paths match 

```js
import NotFound from './NotFound'

const App = () => (
    <BrowserRouter>
      <Switch>
        <Route exact path="/" render={() => <Home user={this.state.user} />} />
        <Route component={NotFound} />
      </Switch>
    </BrowserRouter>
)
```

## URL parameters
* You can specify optional url parameters in the same way you do with Express.js

```js
const App = () => (
    <BrowserRouter>
      <Switch>
        <Route exact path="/" render={() => <Home user={this.state.user} />} />
        <Route path="/courses/:topic/:title" component={Courses}>
        <Route component={NotFound} />
      </Switch>
    </BrowserRouter>
)
```

### The `match` object
* [Docs](https://reacttraining.com/react-router/web/api/match)
* In order to get url parameters inside components, you can use the `match` object
* This is a prop passed to every component that is a child of a `Route` component
* The `match` object contains properties called "path" and "url"
* These can be useful when building nested routes, which will be touched on later

```js
const ExampleComponent = props => {
    const { match } = props;
    const currentPath = match.path;
    ...
}
```

* the `match` object makes url parameters available via the `match.params.paramName`
* You can also get a url parameter with the `useParams` function

```js 
<Route path="/users/:id" ... />

...

const { id } = useParams();
```

## Redirecting
* [Docs](https://reacttraining.com/react-router/web/api/Redirect)
* The `Redirect` component allows you to redirect to another url using the `to` prop that specifies the location to redirect to, and the `from` prop that specifies the matching url on which a redirect should take place 

```js
import { BrowserRouter, Route, Switch, Redirect } from "react-router-dom"

... 

<Switch>
  <Redirect exact from="/this" to="/that" />
</Switch>
```
* You will often want to use the `exact` prop with redirects otherwise the redirect will match with every url that matches the first character in the `from` prop
* When you need to redirect it can be useful to use a Route component that returns the Redirect component from it's `render` prop
* This helps avoid unexpected issues with clicking the link that has the redirect
* Make sure to use the `exact` prop with this otherwise a redirect will happen with every route that also matches the first character of the path specified
* Note that the `Route` component below does not render a view component, it renders a `Redirect` instead

```js
<Route exact path={match.path} render={() => <Redirect to={`${match.path}/html`} />} />
```

## `Link` and `NavLink`
* While you can use anchor tags, this goes against the SPA design. Typically you would use `Link` or `NavLink` as these components will not reload the page
* The main difference between `Link` and `NavLink` is that `NavLink` will by default add a `className` of "active" to the clicked link, allowing you to highlight or apply other styles to the link that has been clicked 
* Both types of links use the `to` prop to specify a path to link to 

```js
import { Link, NavLink } from 'react-router-dom`

const Nav = () => (
    <ul className="main-nav">
      <li>
        <NavLink exact to="/">Home</NavLink>
      </li>
      <li>
        <NavLink to="/about" activeClassName="foo">About</NavLink>
      </li>
      <li>
        <NavLink to="/teachers" activeStyle={{ background: 'lightseagreen'  }}>Teachers</NavLink>
      </li>
      <li>
        <NavLink to="/courses">Courses</NavLink>
      </li>
    </ul>
)
```
* Use the "exact" prop for only the path matching the "to" prop to be active
* If you want to add a custom style to a clicked `NavLink` you can specify it with the `activeStyle` prop and pass it an object containing the custom style, as seen above

## Programmatic routes
* You can create programmatic routes when a user enters information on a form 
* Construct the route from the values of the form inputs, and then push the new url onto the [history](https://reacttraining.com/react-router/web/api/history) object

```js 
export default class Home extends Component {

  /**
   * This method is an example of building a route 
   * programmatically. The user enters information,
   * that information is used to construct a new url,
   * and then that url is pushed onto the history object 
   * In doing so, the app is redirected to a new url
   * When programmatically changing the url you should 
   * use the history object and push the new path onto it
   * instead of using a Redirect component
   */
  handleSubmit = (event) => {
    event.preventDefault();
    const teacherName = this.name.value;
    const teacherTopic = this.topic.value;
    const path = `teachers/${teacherTopic}/${teacherName}`
    this.props.history.push(path);
  }

  render() {
    return (
      <h3>Featured Teachers</h3>
      <form onSubmit={this.handleSubmit}>
        <input type="text" placeholder="Name" ref={input => this.name = input} />
        <input type="text" placeholder="Topic" ref={input => this.topic = input} />
        <button type="submit">Go!</button>
      </form>
    )
  }
}
```

## Nested routes 
* It is possible to nest routes within other main routes, but in this case you do not need to use the `BrowserRouter` component again, because nested routes are by definition already children of `BrowserRouter`
* Using `BrowserRouter` again will cause unexpected behaviour
* You can dynamically add the base uri with `match.path` instead of hard-coding it

```js 
export default const Courses = ({ match }) => (
  <div className="main-content courses">
    <div className="course-header group">
      <h2>Courses</h2>
      <ul className="course-nav">
        <li>
          <NavLink to={`${match.path}/html`}>HTML</NavLink>
        </li>
        <li>
          <NavLink to={`${match.path}/css`}>CSS</NavLink>
        </li>
        <li>
          <NavLink to={`${match.path}/javascript`}>JavaScript</NavLink>
        </li>
      </ul>
    </div>

    <Route exact path={match.path} render={() => <Redirect to={`${match.path}/html`} />} />
    <Route path={`${match.path}/html`} render={() => <CourseContainer data={HTMLCourses} />} />
    <Route path={`${match.path}/css`} render={() => <CourseContainer data={CSSCourses} />} />
    <Route path={`${match.path}/javascript`} render={() => <CourseContainer data={JSCourses} />} />
  </div>
)
```

## Going further
* As always, this set of notes is very basic. Read the [documentation](https://reacttraining.com/react-router/web/guides/quick-start) for a more detailed and complete understanding