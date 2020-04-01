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