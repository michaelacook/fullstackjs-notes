# Modules
- Modules prevent pollution of the global namespace 
- keep the codebase modular and loosely coupled 
- keep information private from other modules
- allow easier debugging

## `module.exports` 
- In node, every file has a `module` object with an `exports` property that is used to define what should be exported from the file
- `exports` exposes the module as an object
- To create a module, create an object-literal, add properties and methods, and export the module from the file

## `require`
- To import the exported module into another file, the `require` function is used in node 
- `require` accepts the path to the file that exports the module
- The path should always be prepended with a `./` but does not need to have the `.js` file extension appended
- `require` will return the module for the specified path, so it can be set to a variable

## `export default`
- ES6 allows for a new syntax for exporting and importing modules 
- Default exports and named exports 
- A default exports allows one module to be exported per file
- Default and named exports are used in client-side development, because node does not support them

## Named exports 
- With named exports, each export from a file is exported as a variable
- Named exports allow you to export individual pieces of data or functions from a file without exporting the entire contents of the file 
- This allows for a file to encapsulate private data and only expose what the developer wants it to
- syntax: `export { specialty, isVegetarian }`, `export const foo = "asdf"`

## `import`
- the `import` keyword is used similarly to `require` in node to import a module
- Slightly different syntax: `import [module] from [path]`

## Named imports 
- To import named exports you destructure them from the exporting file 
- `import { specialty, isVegetarian } from './menu'`

## `Export as`
- Give an alias to an export different from it's variable name 
- `export { variableName as varName }`

## `Import as`
- To import aliased named exports, the alias needs to be referenced in the import statement
- We can also alias an import at the time of import with `as`: `import { isLowSodium as saltFree } from "Menu"
- You can also import an entire module as an alias: `import * as Foo from "./Menu"`
- In this case, all exports in that module become properties of that module and can be referenced as such with dot notation

## Combining export statements 
- Default and named exports can be combined in a single file

## Combining import statements 
- Just as you can have default and named exports, you can import named exports and default exports into the same file