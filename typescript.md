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