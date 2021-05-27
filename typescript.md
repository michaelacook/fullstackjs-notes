# TypeScript

## Basics 
-[documentation](https://www.typescriptlang.org/)
- TypeScript was developed by Microsoft and released as open-source software in 2012 
- It is meant to give JavaScript a type system as well as some other features that make it more appropriate for enterprise software development
- TypeScript is a superset of JavaScript: all JavaScript is valid TypeScript
- TypeScript must be transpiled into JavaScript to run
- TypeScript files have the `.ts` extension 
- Transcompilation is run with the `tsc` command

### Type Inferences
- When a variable is initialized with a particular type, even if that type is not specified, TypeScript infers the type of the variable and will not allow it to be reassigned to another type
- TypeScript always expects variables to remain the same type even when they change. This is how strongly typed languages like Java work
- However if a variable is initialized with no value TypeScript will consider it's type to be `any` and not infer a type
- Variables can also be specified as having `any` type and TypeScript will not throw an error if they are reassigned to a different type
- TypeScript recognizes JavaScript's five primitive data types: 
  - `string`
  - `number` 
  - `boolean` 
  - `null` 
  - `undefined`
- TypeScript also knows the shape of objects and can throw errors and suggest useful corrections when attempting to access methods or properties on an object that don't exist

### Variable Type Annotations 
- Variables can be given an annotation or declaration of type and TypeScript will use it to prevent reassignment to another type  
- Type annotations use the following syntax: 

```ts
let mustBeAString : string;
mustBeAString = 'Catdog';
 
mustBeAString = 1337;
// Error: Type 'number' is not assignable to type 'string'
```

### `tsconfig.json`
- TypeScript can be configured in a number of ways to provide flexibility 
- the `tsconfig.json` file is always placed at the root of the project
- allows you to customize what rules TypeScript applies 
- e.g: 

```json 
{
  "compilerOptions": {
    "target": "es2017",
    "module": "commonjs",
    "strictNullChecks": true
  },
  "include": ["**/*.ts"]
}
```
- when you run the `tsc` command with no arguments, it uses the `tsconfig.json` file to determine what files to transpile

## Functions
- when declaring or defining functions, they often expect arguments of a certain type passed 
- TypeScript makes JavaScript safer by allowing you to specify types for function parameters and function return values 
- function parameters can be type annotated the same way regular variables are

```ts 
function greet(name: string) {
  console.log(`Hello, ${name}!`)
}
```

- TypeScript will throw an error by default if an expected argument isn't passed, but a parameter can be specified as optional using the `?` token 

```ts 
function greet(name?: string) {
  console.log(`Hello, ${name || 'Anonymous'}!`)
}
```

- if a default argument is assigned to a parameter, TypeScript will infer the type of the parameter 
- if no default argument is assigned and no type annotation given, TypeScript will assume the parameter can be `any` type 
- TypeScript will also infer the type of the return value of a function, but a type can be specified explicitly as well 

```ts 
function multiply(x:number, y:number): number {
  return x * y
}
```
    This is a trivial example and only serves to demonstrate syntax.

- to specify the return type of a function that only performs a side effect, use the `void` type 

## Complex Types 

### Arrays and Tuples
- types like objects and arrays can be specified as well 
- to specify an array of specific types, use the following syntax 

```ts 
const foo: string[] = ["make", "it", "so"]
```

- Alternative syntax 

```ts 
const foo: Array<string> = ["make", "it", "so"]
```

- to specify the member types of multi-dimensional arrays, keep adding `[]` to the type annotation n levels 

```ts 
const bar: string[][] = [
  ["this"],
  ["is"],
  ["that"]
]
```

- an array of any dimension can be initialized as an empty array, even though it's member types have been specified 

```ts 
const foobar: number[] = []
// no error here
```

- TypeScript introduces the tuple data structure, which behaves identically to an array but which has a fixed length with it's types must appear in the order specified
- Tuples can contain elements of the same or different types

```ts 
// a tuple 

let foo: [number, string, number] = [5, "asdf", 5]

foo = ["asdf", 5, 6]
// throws an error

foo = [5, "asdf"]
// throws an error
```

- assigning an array to a tuple, even if they have the exact same shape, will throw an error because TypeScript treats tuples and arrays as different types
- TypeScript always infers `[]` as an array by default. Tuples have to be specified since the tuple is the more restrictive type

### Rest Parameters and Spread Operator
- rest parameters are inferred as type `any` by default, but can be specified  

```ts 
const sumAllNumbers = (...numberList: number[]): number => {
  let sum = 0
  for (let i=0; i < numberList.length; i++) {
    sum += numberList[i]
  }
  return sum
}
```

- the spread operator can be used with tuples an an argument to a function that defines the types specified in the tuple in lock and key fashion 

```ts 
function modulo(dividend: number, divisor: number): number {
  return dividend % divisor;
}
 
const numbers: [number, number] = [6, 4];
 
// Call modulo() with a tuple
console.log(modulo(numbers));
// Error: Expected 2 arguments, but got 1.
// Prints NaN
 
// Call modulo() with spread syntax
console.log(modulo(...numbers));
// No error, prints 2
```
### Custom Types
- TypeScript allows developers to create custom types that can be enforced in addition to primitive types

#### `Enum` Type 
- `enum`s represent an **enumeration** of all the values a variable could have
- allows you to limit assignment of a variable to only pre-determined possibilities
- e.g

```ts
enum Direction {
  North,
  South,
  East,
  West,
  Northwest,
  Northeast,
  Southwest,
  Southeast
}

let direction: Direction = Direction.North
```

- if your program requires direction input and you don't want the program to accept anything but what you define as a valid direction, the above `enum` will come in handy
- under the hood the enum assigns each value to an index 

```ts 
enum Direction {
  North = 0,
  South = 1,
  East = 2,
  West= 3,
  Northwest = 4,
  Northeast = 5,
  Southwest = 6,
  Southeast = 7
}

Enum.North === const direction: Direction = 0
```

- the number values can also be set manually by the programmer
- this is a numeric `enum` but string `enum`s are also possible, and are recommended because they are more strict 

```ts 
enum Direction {
  North = "NORTH",
  South = "SOUTH",
  East = "EAST",
  West = "WEST",
  Northwest = "NORTHWEST",
  Northeast = "NORTHEAST",
  Southwest = "SOUTHWEST,
  Southeast = "SOUTHEAST"
}
```

- values assigned do not have to be uppercase, but it is the recommended convention 
- numeric `enums` are less safe, as they allow a variable of the `enum` type to be assigned direcly to a number, and will allow numbers outside the range of indices in the specified `enum` to be assigned, opening up the possiblity of introducing avoidable bugs 
- string `enums` can only be assigned to a value directly specified in the `enum`: 

```ts 
enum Direction {
  North = "NORTH",
  South = "SOUTH",
  East = "EAST",
  West = "WEST",
  Northwest = "NORTHWEST",
  Northeast = "NORTHEAST",
  Southwest = "SOUTHWEST,
  Southeast = "SOUTHEAST"
}

const direction: Direction = "EAST" 
// Type Error

const direction: Direction = Direction.East 
// correct
```

#### `Object` Types 
- `Object` types are the most common non-primitive type 
- allow fine level control 
- the object type can be specified with the following syntax 

```ts 
let car: {make: string, model: string, year: number}
```

- however, it is not necessary to specify the type this way every time, as it would lead to a potentially huge amount of repetition
- types can be given a type alias and reused 

```ts
type Student = {
  name: string,
  age: number,
  courses: string[]
}

const michael: Student = {
  name: 'Michael', 
  age: 31, 
  courses: ['JavaScript', 'TypeScript']
}
```

- type aliases can be used with any custom or primitive type

#### `Function` Types 
- You may want to strongly type a function, especially in cases where you are writing a function that takes a callback 
- A function type can be specified using a type alias that defines parameters and a return type 

```ts 
type NumberArrayFunction = (numberArray: number[]) => number 
```

- it doesn't matter what the parameter name(s) are, as long as they are typed
- any function that uses the function type specified must accept the same number and type of parameters and return the same type as specified
- this is extremely useful when you need to enforce the parameters and return value of a callback

#### Generics 
- generics allow you to specify a complex type like an object, array or function where the inner types in the structure have not yet been specified, and can be specified at the time the developer uses the type 
- put another way, generics allow the creation of reusable blocks of code that can be used with different types

```ts 
type Collection<T> = {
  name: string,
  quantity: number,
  content: T[]
}

let primeNumberCollection: Collection<number> = {
  name: 'First 5 Prime Numbers',
  quantity: 5,
  content: [2, 3, 5, 7, 11]
}
```

- in the example, the property `content` can be given any type as needed, and is specified when the type is used as an annotation by passing it between brackets `<>`
- in general, the placeholder for the generic type is `T` but this is just convention 
- functions can be generic and allow for any type to be passed to the annotation when the function is called or assigned to a variable 

```ts 

// This is a generic function type alias
function findMiddleMember<T>(members: T[]): T {
  return members[Math.floor(members.length / 2)]
}

console.log(findMiddleMember<string>(['I', 'am', 'very', 'happy']))
```
- Generics are useful when you need a function or data structure that may use different data types at different times, but when you do pass it a data type you want it to be type-safe 
- Generics are also very useful for functions that return promises 
  - An async function or a function that is wrapped in a promise will always return a promise, which could resolve to any data type 
  - You could add the type `Promise` to a function, but that still doesn't tell you what type the promise will actually resolve to. So specifying a generic is useful because it allows you to make the resolve value type-safe without knowing what the type will be in advance 
  - e.g 
  ```ts
  async function queryEndpoint(url:string, method:string, payload?:object, query?:string): Promise<T> {
    try {
      const data = await api.query(url, method, payload)
      return data
    } catch (err) {
      return err
    }
  }

  const userId = queryEndpoint("/users/", "POST", {
    name: "John Doe",
    email: "email@email.com, 
    password: "asdf"
  })<number>

  const user = queryEndpoint(`/users/${userId}`, "GET")<object>
  ```
  This isn't a fully realistic example but serves to show the usefulness of a generic in a situation like this

## Union Types