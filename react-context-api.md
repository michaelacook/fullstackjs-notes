# React Context API
* [Documentation](https://reactjs.org/docs/context.html)
* In a React application you may end up having a deeply nested component hierarchy in which props are passed down through one or many components that do not actually need access to those props, just so that data can reach a more deeply-nested component
* This is called **prop drilling**
* The Context API allows you to avoid this problem by creating context that is available directly to any component you decide, regardless of how deeply nested that component is
* Context is not a solution that should be applied in every case. But when Redux is too much but you still need some state management, context can be useful 

## Setting up a Provider and Consumer
* To create context in a React application, you first create a Provider and Consumer
* The Provider provides the data you want to make directly available via context, and the Consumer wraps components that consume or "subscribe" to data provided by the context Provider
* To create the Provider and Consumer, you use the `React.createContext()` method
* Create a separate file and import React and initialize your context variable:

```js 
import React from 'react';

const ApplicationContext = React.createContext();
```

* Then destructure the Provider and Consumer from the context variable and export them:

```js 
const { Provider, Consumer } = ApplicationContext;

export Provider;
export Consumer;
```

## Adding the Provider
* The Provider should generally go as high up the component hierarchy as possible so this means your root component
* The Provider looks like an element tag that wraps everything that the root component renders in its `render` method
* The provider has a `value` prop that contains any data you wish to be available in context 
* All children of the Provider have access to the data in the Provider's `value` prop and will re-render whenever it changes

```js
import { Provider } from './path/to/provider';

...

render() {
    return (
        <Provider value={this.state}>
            <Header />
            <PlayerList />   
            <AddPlayerForm />
        </Provider>
    )
}
```

* You can pass state in the `value` prop or you can pass a custom object containing only the data you want
* You can also pass references to methods
* E.g: 

```js
<ScoreboardContext.Provider value={{
    players: this.state.players,
    
    // references to methods here
    actions: {
        changeScore: this.handleScoreChange,
        removePlayer: this.handleRemovePlayer,
        addPlayer: this.handleAddPlayer
    }
}}>
...
</ScoreboardContext.Provider>
```

## Adding Consumers 
* Consumers have direct access to the data specified in the `value` prop passed to the Provider
* Any component can be made a Consumer of the context by wrapping the elements and/or components the component renders in Consumer tags that accept a prop called the Render Prop
* The Render Prop is a function that accepts the context as an argument and returns the component with access to the context 
* E.g:

```js
import { Consumer } from './path/to/context';

...

return (
    <Consumer>
    {context => {
        return (
            <React.Fragment>
            {context.players.map((player, index) =>
                <Player 
                key={player.id.toString()} 
                index={index}         
                />
            )}
            </React.Fragment>
        )
    }}
    </Consumer>
)
```
* Every element returned from the Render Prop inside the Consumer tags will have access to the context data no matter how deeply nested
* It is also possible and desirable to destructure the context parameter in the Render Prop. E.g: 

```js
return (
    <Consumer>
        {/* Destructure the context parameter */}
        { ({ players }) => (
        <React.Fragment>
            {players.map( (player, index) =>
            <Player 
                key={player.id.toString()} 
                index={index}         
            />
            )}
        </React.Fragment>
        )}
    </Consumer>
);
```

## The Higher Order Component pattern
* Higher Order Components are functions that take a component and return a new component
* Instead of exporting a Provider object to wrap the contents of the root component's `render` method, it is possible to create a class component called `Provider` that maintains application state and returns a component wrapped in the Provider 
* In the component rendered by the `Provider` component, you pass `this.props.children` inside the Provider
* [Article on `props.children`](https://codeburst.io/a-quick-intro-to-reacts-props-children-cb3d2fce4891)
* This makes any child components passed into this component children, and therefore able to access context
* You export the `Provider` component and import it into your entry point and pass it to `ReactDOM.render` with your root level `App` component as a child
* The `Provider` that renders the Provider object thus becomes the new root level component, and all of it's children have access to context
* E.g: 


```js 
import React, { Component } from 'react';

const ApplicationContext = React.createContext();

export class Provider extends Component {
  state = {
    ...
  }

  render() {
    return (
      <ApplicationContext.Provider value={this.state}>
        { this.props.children }
      </ApplicationContext.Provider>
    );
  }  
}

export const Consumer = ApplicationContext.Consumer;
```

* Then you can simplify your old root level component to be a simple stateless functional component: 

```js 
import React from 'react';
import Header from './Header';
import PlayerList from './PlayerList';
import AddPlayerForm from './AddPlayerForm';

const App = () => {
  return (
    <div className="scoreboard">
      <Header />
      <PlayerList />   
      <AddPlayerForm />
    </div>
  );
}

export default App;
```

* Then you render `App` as a child of `Provider` in your entry point: 

```js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from './components/Context';
import App from './components/App';
import './index.css';

ReactDOM.render(
  <Provider>
    <App />
  </Provider>, 
  document.getElementById('root')
);
```

* This pattern can keep things cleaner, and it works well for an application in which many components need to share the same data and would thus require significant tedious prop drilling
* As always, this noteset is fairly basic and serves as a starting point. It is encouraged that you read the documentation for a more complete discussion of the Context API
