# JavaScript Array Iteration Methods
* (note: this set of notes assumes basic knowledge of the  `array` data type in JavaScript)
* There are a number of methods for iterating over arrays that could be better or worse in certain situations
* At it's most basic, **iteration** is moving a pointer through an array to find and/or alter data in some fashion

## `for` loops vs. `forEach` loops
* `forEach` is a loop that accesses each item in an array
* Syntax: `array.forEach(callback);`
* E.g:

```js
const foo = [1, 2, 3, 4, 5];

foo.forEach(item => {
    console.log(item);
});
```
* To do the same thing with a `for` loop use the following syntax:

```js
const foo = [1, 2, 3, 4, 5];

for (let i = 0; i < foo.length; i++) {
    console.log(foo[i]);
}
```

### `forEach` benefits:
* Simpler syntax, easier to write and understand
* Eliminates certain bugs that can creep into `for` loops (i.e infinite loops that crash the browser)
* Automatically increments and breaks the loop thus avoiding incrementing mistakes

### `forEach` cons:
* Can't `break` out of a loop early as you can in a `for` loop
* `forEach` is useful when you need to iterate over every item in an array and do not need to have an early `return` or `break`
* `for` loops give more control but also are more error-prone, less concise, harder to read

* `forEach` in action:

```js
const names = ['Selma', 'Ted', 'Mike', 'Sam', 'Sharon', 'Marvin'];

const newNames = [];
names.forEach(name => {
    if (name[0].toLowerCase() == 's') {
        newNames.push(name);
    }
});

console.log(newNames);
[ 'Selma', 'Sam', 'Sharon' ]
```

### `forEach` `index` and `array` parameters
* [`forEach` documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
* The index at the current pointer position in a `forEach` loop is accessible with an option `index` parameter, as is the array on which the loop is operating
* E.g:

```js
const names = ['Selma', 'Ted', 'Mike', 'Sam', 'Sharon', 'Marvin'];

names.forEach((name, index) => console.log(`${index + 1}) ${name}`));
1) Selma                                                          
2) Ted                                                                   
3) Mike                                                                   
4) Sam
5) Sharon                                                            
6) Marvin
```

* The `index` and `array` parameters are available on all array methods covered in the course

## Array manipulation

### `filter()`
* Returns a new array containing only the items you did not want to be removed or filtered out
* Keeps items at their original index
* The syntax for `filter()` is similar to the syntax for `forEach()`

```js
array.filter(callback);
```
* The callback passed to `filter()` must return either `true` or `false`; on `true` the item at the current point in the iteration will be added to the new array, otherwise it will not, thus "removing" it
* Note again that the original array is not affected
* E.g:

```js
const numbers = [1, 2, 3, 4, 5];

const noThree = numbers.filter(number => number != 3);

console.log(noThree);
[1, 2, 4, 5]
```
* (note: in ES6 you can skip the `return` key if there is only one line of code in the function body, and since the expression in the function body evaluates to `true` you don't need to return `true`, just return the condition)

### `map()`
* Used to transform each item in an array into something else
* Returns a new array, leaving the original unchanged
* The new array contains the same number of elements as the original array
* Example use-cases:
    * Turn an array of celsius temperatures to farenheit
    * Capitalize or pluralize a list of strings
    * Convert an array of currency to another currency
    * etc
* Syntax: `array.map(callback)`
* E.g:

```js
const strings = ['1', '2', '3', '4', '5'];

const numbers = strings.map(string => parseInt(string));

console.log(numbers);
[1, 2, 3, 4, 5]
```

### `reduce()`
* Allows you to compute, or reduce all values in an array down to a single item
* Executes a reducer callback that you provide
* [`reduce()` docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
* Possible use-cases:
    * Returning the sum of all values in an array
    * Finding out how many names in an array have more than 6 characters
* Syntax: `array.reduce(callback(accumulator, currentValue), initialValue);`
* Requires an accumulator argument that accumulates the callback's return values
* Also requires a currentValue argument representing the item currently being processed in the array
* E.g:

```js
const prices = [6.75, 3.10, 4.00, 8.12];

const total = prices.reduce((sum, price) => sum + price, 0);

console.log(total);
21.97
```

* The 0 after the callback is the initialValue argument, which sets the initial value of the accumulator. If no initialValue is given, then the initial value is the first element in the given array and iteration will skip over it and begin at the second index value
