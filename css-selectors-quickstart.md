# CSS Selectors Quickstart 

## Types of selectors 
* There are a number of ways you can apply css rules to html

### Type selectors 
* Selecting elements based on their tag type 
* E.g `p { color: green; }`

### ID selectors 
* Select an element based on it's unique element ID 
* E.g `#example { font-size: 2.5em; }`
* Each html file can only have an ID name once. They must be unique

### Class selectors 
* Select elements based on their class attribute  
* Many elements can share the same class 
* E.g: 

```css
 /* Make all elements with .btn class rounded with blue background */

.btn {
    padding: 25px 10px;
    color: white;
    background-color: lightblue;
    border-radius: 2px;
}
```

### Attribute selectors 
* An element can have any number of different attributes, like id, class, title, etc
* To target an attribute use square brackets []
* E.g: `[title] { color: seagreen; }`
* You can target only elements of a certain type by prepending the attribute selector with the element type
* E.g `div[title] { color: crimson; }`
* You can also be even more specific and target only elements with a certain type, attribute and attribute value
* E.g `div[title='toolTip'] { color: cornflower blue; }`
* Attribute selectors are useful for targeting form elements since form elements use a lot of attributes

## Pseudo-classes and Combinators 
* Pseudo-classes are selectors that match elements based on their state and user actions 
* One of the most common is :hover for when the user is hovering over an element with the mouse 
* Combinators select elements based on their relationship to one another 
* For instance, targeting descendant elements of a specific element 

### Dynamic pseudo-classes 
* Pseudo-class selectors always specify the element or other selector followed by a colon ':' then the pseudo-class 
* E.g `a:hover { ... }`
* There are also `:link`, `:visited`, ':active' and other pseudo-classes 
* (note: `:link` and `:visited` only work on anchor tags with an `href` attribute)
* [Pseudo-classes documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)
* The `:first-child` pseudo-class targets the first child in a set of sibling elements
* The `:last-child` pseudo-class targets the last child is a set of sibling elements
* The `:nth-child()` pseudo-class accepts an argument and targets a specific child element 
    * It can accept `even`, `odd` or a number for position of the element 
    * If `even` or `odd` is specified it will target all elements with either an even or odd position index
    * `:nth-child()` uses a 1-based index system rather than the more common 0-based index system

## Descendant selectors 
* It is possible to target elements based on their relationships in an HTML document 
* Selectors can be combined to create descendant selectors 
* To target a css rule at only certain elements that descend from a parent element, use the following syntax: 

```css
parent child {
    ...
}
```

* E.g: 

```css
header span {
  color: darkviolet;
  font-size: 26px;
}
```

* E.g: 

```css
#main-nav a {
  text-decoration: none;
}

.main-header .headline {
  color: white;
  font-size: 72px;
}
```
* You can target elements as deeply nested as you like 
* Descendent selectors are not limited to type selectors but can target class or ID as well

## Combinators 
* Combinators describe a relationship between two or more elements and can make your selectors more specific 
* They allow you to target an element's siblings, descendants or direct children
* The whitespace in a descendant selector is a combinator that signifies a descendant relationship 
* You can also use the `>` combinator to target direct children 
* `+` combinator to match an element's immediate sibling
* `~` combinator for targetting general siblings. This combinator targets all direct siblings
* Immediate and general sibling combinators only target siblings that follow an element, not elements that precede it

## CSS Selectors and JavaScript
* Selectors are used in JavaScript to get and manipulate elements on a web page
* E.g `document.getElementById()` and `document.querySelector()`
* Using selectors combined with built-in JavaScript APIs for interacting with html, you can grab hold of, read, manipulate, change, and create new html and content on a web page 
* JavaScript can also be used to select and then program elements on a page to respond to different kinds of user interactions

### Prefix classes for JavaScript 
* It's a best practice to not use the same selector for your CSS rules and your JavaScript files 
* For instance, use one class name to style an element, and another class name to apply JavaScript to the element 
* E.g: 

```js 
<button class="btn-lg js-modal">Click Me!</button>

.btn-lg {
  font-size: 2.5em;
  display: block;
}
```
* In the example above, the `btn-lg` class is used to style the element, and `js-modal` would be used to select the element for scripting 
* This helps keep your CSS and JavaScript separate, and keeping different concerns separated is a best practice

### Custom data attributes 
* HTML comes with built-in attributes, but you can also specify your own custom attributes and use CSS and JavaScript to select them
* Custom attributes must be prefixed with `data-` and be given any name you want 
* Separation of concerns: data attributes allow you to select an element for manipulation with JavaScript, but use other attributes or CSS classes to style with CSS
* Separating these concerns allows you to change the CSS without affecting the JavaScript. This makes your program less likely to break if changed

## Best Practices 
* Ids are not reusable and they should only be used for elements that will really never change
* Classes are reusable and a single element can be as many classes as you want, making them very useful 
* Don't use the same selectors for both styling and for scripting--this couples your CSS with your JavaScript and violates the separation of concerns 
* If one and only one element needs to match your selector, IDs are OK; otherwise, when in doubt use CSS classes or data attributes 
* Use the BEM naming convention for writing CSS classes
    * [BEM](https://en.bem.info/)
    * [BEM for beginners](https://www.smashingmagazine.com/2018/06/bem-for-beginners/#the-basics-of-bem)
    [CSS with BEM](https://en.bem.info/methodology/css/)
    * [Treehouse BEM video](https://teamtreehouse.com/library/modular-css-with-sass/sass-and-bem/the-bem-pattern)
    * BEM - Block, Element, Modifier 
    * Describe markup in terms of blocks, elements, and modifiers 
    * The block is the component, or parent element 
    * The element is the child element in the component or block
    * The modifier is the class that makes changes to a block and it's elements 
* Use a dedicated prefix or "hook" such as `js-` when specifying a class that will only be used by JavaScript. This maintains separation of concerns