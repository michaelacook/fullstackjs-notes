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


## Combining array methods

* `map()` and `filter()` can be chained together to be useful for more complex iteration tasks
* For example, transforming an array with `map()` and then filtering it with `filter()`
* E.g:

```js
const arr = [1,2,3];

const smallerArr = arr.filter(number => number !== 2).map(number => number + 1);

console.log(smallerArr); // => [2,4]
```
* As long as an array method returns an array, you can chain link another array method onto the end if need be. This is referred to as **method chaining**

#### Using `reduce()` to return a new object from an array of objects

```js
const users = [
  {name: 'Samir', age: 27},
  {name: 'Angela', age: 13},
  {name: 'Beatrice', age: 49}
];

const usersObject = users.reduce((acc, person) => {
    acc[person.name] = person.age;
    return acc;
}, new Object());

console.log(usersObject);
{ Samir: 27, Angela: 13, Beatrice: 49 }
```

* Note that returning the accumulator in the same line that the accumulator is added to gives an unexpected result. To avoid this, perform your action on the accumulator first, then return on the next line

#### Using `filter()` and `map()` to return an array of objects

```js
const userNames = ['Samir', 'Angela', 'Beatrice', 'Shaniqua', 'Marvin', 'Sean'];
    // Result: [{name: 'Samir'}, {name: 'Shaniqua'}, {name:'Sean'}];

const newUserNames = userNames
    .filter(name => name.charAt(0) === 'S')
    .map(person => ({name: person}));

console.log(newUserNames);
[ { name: 'Samir' }, { name: 'Shaniqua' }, { name: 'Sean' } ]
```

* Note that when returning an object-literal inside the callback, it is necessary to surround it in parentheses otherwise JavaScript cannot distinguish between the object-literal and the function body and will return `undefined`

#### Using `filter()` and `reduce()` to get the highest number of a subset

```js
const products = [
  { name: 'hard drive', price: 59.99 },
  { name: 'lighbulbs', price: 2.59 },
  { name: 'paper towels', price: 6.99 },
  { name: 'flatscreen monitor', price: 159.99 },
  { name: 'cable ties', price: 19.99 },
  { name: 'ballpoint pens', price: 4.49 },
];

    // Result: { name: 'paper towels', price: 6.99 }

  const highest = products
    .filter(product => product.price < 10)
    .reduce((acc, product) => {
        if (acc.price > product.price) {
            return acc;
        } else { return product; }
    });

console.log(highest);
{ name: 'paper towels', price: 6.99 }
```

## Flattening arrays
* It is common to come across arrays that contain arrays, and it is also common to need to 'flatten' the array or place all the items in each internal array into one big array
* There are many ways to do this, but `reduce()` works well

```js
const movies = [
  ['The Day the Earth Stood Still', 'Superman', 'Ghostbusters'],
  ['Finding Dory'],
  ['Jaws', 'On the Waterfront']
]

const flattenedMovies = movies.reduce((acc, cur) => [...acc, ...cur], []);

console.log(flattenedMovies);
["The Day the Earth Stood Still", "Superman", "Ghostbusters", "Finding Dory", "Jaws", "On the Waterfront"]
```
* In the example above, at each iteration the interal array is spread out and placed inside the accumulator, eliminating the internal arrays

#### `...` (spread) operator
* Put simply, the spread operator puts the elements of an array into another array, allowing you to avoid adding an array as itself an element in an array
* E.g:

```js
const arr1 = [1, 2, 3, 4, 5];

const arr2 = [];

arr2.push(...arr1);

console.log(arr2);
[1, 2, 3, 4, 5]
```

* The spread operator has a lot of applications with arrays and objects. [Read the documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

* A more complex example:

```js

const users = [
  {
    name: 'Samir',
    age: 27,
    favoriteBooks:[
      {title: 'The Iliad'},
      {title: 'The Brothers Karamazov'}
    ]
  },
  {
    name: 'Angela',
    age: 33,
    favoriteBooks:[
      {title: 'Tenth of December'},
      {title: 'Cloud Atlas'},
      {title: 'One Hundred Years of Solitude'}
    ]
  },
  {
    name: 'Beatrice',
    age: 42,
    favoriteBooks:[
      {title: 'Candide'}
    ]
  }
];

const favouriteBooks = users
    .map(user => user.favoriteBooks.map(book => book.title))
    .reduce((acc, cur) => [...acc, ...cur], []);

console.log(favouriteBooks);
["The Iliad", "The Brothers Karamazov", "Tenth of December", "Cloud Atlas", "One Hundred Years of Solitude", "Candide"]
```

* First the part of the example, the array contained as a property in each member object is extracted and put into a new array using `map`. This produces a multidimensional array:

```js
[
    ["The Iliad", "The Brothers Karamazov"],
    ["Tenth of December", "Cloud Atlas", "One Hundred Years of Solitude"],
    ["Candide"]
]
```

* The second part of the example then flattens the resulting multidimensional array
