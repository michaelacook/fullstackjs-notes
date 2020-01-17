# npm
* npm is a **package manager** for installing libraries, frameworks and commandline tools written with JavaScript
* A package manager allows you to find, install, uninstall, and update libraries in a standardized way via the command line rather than searching the web and manually downloading files to include in your project
* A package, or library, is a reusable bundle of code you can use in your own project
* npm packages for node.js projects are called **modules** - you may hear 'package' and 'module' used interchangeably
* npm doesn't just have to be used for node projects, it can also be used for font-end dependencies

## Finding packages
* Find packages on the [npm website](https://www.npmjs.com/)
* Factors to consider when deciding between different packages that do the same thing:
    * Popularity
    * Number of releases and how active
    * Number of issues and contributors on Github
    * Other Github indicators of activity and stability
        * tests, etc
* Search for packages on google

### Installing local packages
* This option is for installing packages locally for a particular project
* `$ npm install [package]`
* There are a number of ways to install a package and they can be viewed by running the command `$ npm install -h`
* When installing a package or module, npm creates a directory called `node_modules` and this is where packages are installed. If the package being installed has any external dependencies, npm will install those dependencies in a subdirectory also called `node_modules`
* Once a package is installed, it can be accessed in your project by using `require()` on any functions, classes, etc that you need from the package
    * It is a good practice to read through the package's documentation or readme file to get a sense of how to use it
* It is not necessary to include the file path to the package's source code when using `require()` and this is because the `"main"` key in `package.json` file tells node which file path to use when requiring the module, so you don't have to. Just use:

```js
const package = require(packageName);
```

### Installing global packages
* This option is for installing software that you will have access to globally (typically on the command line) instead of only in a particular project. Suitable for commandline tools
* Run `$ npm install [package] -g`
* The `-g` option specifies global installation
* [permissions issues](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)


## `package.json`
* The `package.json` file provides a way to manage your project's dependencies in a simple way
* This file goes in the root directory of your project
* To create the `package.json` file run `$ npm init` and follow instructions in the terminal
* To make sure a new dependency is added to the `package.json` file when you install that dependency, use the `--save` commandline option after the install command `$ npm install [package] --save`
* It also tells other developers which dependencies they need to run your code
* You wouldn't want to host all your dependencies in a github repository. Instead, the `package.json` file provides a manifest of all the dependencies someone else will need to install locally for your project
* To tell git not to track your dependencies, you can make a `.gitignore` file
* To specify in your `.gitignore` file not to track your dependencies, write 'node_modules/' in the file
* If you are unable to open the file, first change it to a `.txt` file and then change it back after editing
* To install all the dependencies specified in `package.json`, just run `$ npm install` in the root directory of your project and npm will automatically install all dependencies specified in the manifest`

##### Dev dependencies
* There are dependencies that your app requires to run, but there are also dependencies that you use in the process of building a project. These are referred to as dev dependencies. A good example is a test framework
* Listing dev dependencies as dev prevents others from having to install unnecessary files that aren't needed to run your app. For instance, the test modules you used are not needed when someone clones and runs your app, or when you deploy it to a production server
* To install dev dependencies run `$ npm install [package] --save-dev`
* An example is the popular JavaScript testing library Mocha
* To install a project without dev dependencies included, you can specify that the installation is for production using the node environment variable: `$ NODE_ENV=production npm install`
* Setting the node environment variable to production tells npm to leave out dev dependencies and only install what's required to run the app. Otherwise, npm assumes you are running an install for development purposes  

## Updating dependencies
###### Semantic versioning
* Semantic versioning is the three numbers you see for the version of an app or library
* E.g `version 1.1.2`
* `[major release].[minor release].[patch release]`
    * Major releases represent a full new version that is no longer compatible with previous versions
    * Minor releases represent a release that is generally backward's compatible with the last minor release, but isn't always
    * Patch releases represent bug fixes and are backwards compatible
* The `^` caret symbol in front of a semantic version means the latest minor release up till the next major release
* E.g `"^0.8.3"` If you wanted to install a package with this version it would install the last minor release without going to the next major release
    * In other words, you could install 0.9 but not 1.0
* The `~` symbol means install the latest patch release up till the next minor release
* E.g `"~0.8.3"` will install the latest patch all the way up to 0.8.9 but not install 0.9.0.
* It's a good idea to only update for newest patch or minor releases, since updating to a new major release will break your app

#### Updating
* To find out which dependencies are out of date run `$ npm outdated` and npm will print to the console a list of packages for which newer versions exist
* To specify which version you want to install, specify the version in `package.json` and specify any upper limits you want with `~` or `^` and run `$ npm update`
* To update a globally-installed package run `$ npm update [package] -g`
    * This could update to a new major release
* npm is also an npm package, so the process described is also how you would update npm itself

#### Uninstalling
* Run `$ npm uninstall [package] --save`
* Without the `--save` flag the package will be uninstalled but not removed from `package.json`
* To uninstall a dev dependency run `$ npm uninstall [package] --save-dev`
* To uninstal a global package run `$ npm uninstall [package] -g`

## npm as a task runner
* Some tasks in software development are repetitive and can be done with a task runner rather than by a human
* Some common tasks include:
    * Running a test suite
    * Compiling/transpiling Sass, TypeScript, CoffeeScript
    * Starting a web server
    * Starting a worker
* Grunt.js and Gulp.js build systems are great task runners but are also large and can take a lot of setup. For many things, npm will do
* npm tasks are called scripts and are added to the `"scripts"` property in `package.json`. They have a name and a command
* Once you've added a script to `package.json` you can run the script any time from the command line using `$ npm run [script-name]`
* Repetitive tasks can therefore be semi-automated
* npm can run any binary or commandline application

###### Built-in tasks
* Tasks that come built into npm and are common to most projects
    * E.g "test"
* To run a built-in task use `$npm [task-name]`. There is no need to use the `run` keyword for built-in tasks

###### Arbitrary tasks
* Tasks you specify and write the script for
* require the `run` keyword
* Many projects won't need to specify arbitrary tasks because npm's built-in tasks cover many common software development needs

#### Creating built-in tasks
* Set the name of the task as the key in the `"scripts"` property, and set the script or executable command as the value
* E.g:

```js
"scripts": {
    "test": "mocha"
},
```
* You can then run `$ npm test` and run the test (this assumes you have a testing library installed and set up).

#### Creating arbitrary tasks
 * Set the name of the task in `package.json "scripts"`
 * Set the value to the name key as the command or set or commandline tasks with arguments
 * E.g:

 ```js
 "scripts": {
     "uglify": "uglifyjs src/models/* src/frontend.js -m -c -o built/app.js"
 },
 ```

* To run: `$ npm run uglify`
* You could also specify any number of commandline tasks that you need done frequently as an arbitrary task, like copying files, common git commands, etc
* You can also run other npm tasks as a task. This allows you to run multiple tasks at once that have previously been specified
    * If you specify more than one command in a task make sure to use `&&` between them to specify that npm should do this, **and** that sequentially. A single `&` will run the commands simultaneously, which may or may not be okay depending on what you're doing
* To see all built-in tasks npm can do, run `$ npm help scripts`

## Getting help
* npm has a lot more commands than are covered in this set of notes, so to learn about more commands or get help using a command run `$ npm help [command]` to get a help page
* To get a list of all npm commands run `$ npm`
* You can also read the npm [documentation](https://docs.npmjs.com/)
