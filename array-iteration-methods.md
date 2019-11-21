# JavaScript Array Iteration Methods
* (note: this set of notes assumes basic knowledge of the  `array` data type in JavaScript)
* There are a number of methods for iterating over arrays that could be better or worse in certain situations
* At it's most basic, **iteration** is moving a pointer through an array to find and/or alter data in some fashion

## `for` loops vs. `forEach` loops
* `forEach` is a loop that accesses each item in an array
* Syntax: `array.forEach(item => callback);`
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
