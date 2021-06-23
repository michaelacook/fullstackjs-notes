# Node.js Basics
* Node is great for building scalable and high-performance server-side applications that are capable of handling a lot of connections
* Node is a runtime environment that allows JavaScript to run outside of the web browser, making JavaScript available just about anywhere and capable of powering a wide variety of applications

## Running Node
* To run a JavaScript file with node, in Bash run `$ node [file.js]` and node will execute the file
* To enter the node REPL (read-evaluate-print loop), in the terminal run `$ node` and to exit, use `Ctrl-C` twice, `Ctrl-D` once or `.exit`

```js
console.log('Hello, world!'); // log out messages
console.error('Oops, something went wrong!'); // log out error message
console.dir({ name: "Michael", age: 29 }); // prints human-readable data
```
`$ node example.js`
```
Hello, world!
Oops, something went wrong!
{ name: Michael, age: 29 }
```

### JavaScript outisde the browser
* JavaScript's native objects are available in node because they are native to Javascript, but browser-specific host objects are not available, like the `Document`, `Window`, `History`, and `XMLHttpRequest`
* Chrome's V8 JavaSript engine had great performance levels, so they took it out of the browser and stripped it of browser APIs and made it avaiable to run JavaScript anywhere
* Node has host objects like `http`, `https`, `fs` (file system), `url`, `os` and more in it's environment

### Why use node.js?
* Most backend languages and frameworks are only capable of handling single tasks at a time, and are therefore slower and less efficient
* These are known as blocking languages, since each request blocks the handling of other requests until finished
* To get around this, developers usually need to host the web application across multiple servers in order to be able to handle many simultaneous requests
* JavaScript and the node.js platform are non-blocking, so they require less resources and respond faster

## Node globals
* Global objects are accessible anywhere in any scope in the node runtime
* [Read about node global objects](https://nodejs.org/dist/latest-v13.x/docs/api/globals.html)
* The `console` object is a global object in the node runtime
* The `require()` function is a global function used to import modules from external dependencies, internally from nodes modules, or modules we create 
    * One useful module we can require is the `path` module which is useful for working with file paths
    * The `__dirname` is a globally available path to the working directory, and `__filename` is a globally available path to the current file
         * (note: these are not available in the repl)
    * To get the base file name from a path we could do the following: 

```js
const path = require('path');

console.log(`${path.basename(__filename)}`);
```
* `process` is another global in the node runtime
    * it contains information about the current process and tools to help us work with it
    * Read environment information, read env variables, communicate with the terminal via stdin and stdout processes, and exit the current process  
    * You can access commandline arguments with `process.argv` and it will return an array that contains all the information (file paths, commandline args) that were used to run the process
        * The first path you will see is the path to node itself, the second is the path to the current file, then any commandline arguments after that
        * When capturing commandline arguments, using flags is useful. Consider the following program: 

```js 
const grab = flag => {
    let indexAfterFlag = process.argv.indexOf(flag) + 1;
    return process.argv[indexAfterFlag];
}

const greeting = grab("--greeting");
const user = grab("--user");

console.log(`${greeting} ${user}`);
```
* In the above program you can run `$ node [file] --user Michael --greeting "Hello there"` and the program will print "Hello there Michael" to the console 
* commandline flags are a useful way to specify the role of commandline arguments

### `stdout` and `stdin`
* stdin is a property of the global `process` object and represents  a stream where data can be entered into a program
* stdout is also a property of `process` that represents data output from the current process
* These streams have events that can be listened for, such as 'data' and 'exit'
* See a small program that uses the standard output and standard input: 

```js 
const questions = [
    "What is your name?",
    "What would you rather be doing?",
    "What is your preferred programming language?"
];

const ask = (i = 0) => {
    console.clear();
    process.stdout.write(`\n\n\n${questions[i]}`);
    process.stdout.write(` > \n`);
}


const answers = [];

ask();

/* listener
    the .on() method is how you listen for an event 
    'data' is an event and it's when data gets passed to the program via 
    the commandline or something else
    data comes into the program in the form of a buffer, so it has to be 
    converted into a string or other useful data type
 */
process.stdin.on('data', data => {
    answers.push(`${data.toString().trim()}`);
    if (answers.length < questions.length) {
        ask(answers.length);
    } else {
        process.exit();
    }
});

/* exit is a process event that can be listened for */
process.on('exit', () => {
    console.clear();
    console.log('Your answers were: \n');
    answers.forEach(answer => console.log(`${answer}\n`));
});
```

* Asynchronous APIs like `setInterval` and `setTimeout` are also available globally in the node runtime

## Node modules
* Node has many internal modules you can work with. This set of notes covers core modules that are good for all developers working with node to be familiar with. Read the documentation and look through the examples to familiarize yourself

### Core modules 
* The `path` module is an important module for working with file and directory paths
    * [`path` module docs](https://nodejs.org/dist/latest-v13.x/docs/api/path.html)
* The `util` (utilities) module is another important module that provide a number of helper functions for node's own APIs
    * [`util` module docs](https://nodejs.org/dist/latest-v13.x/docs/api/util.html)
* The `v8` module is another core module that exposes APIs that are specific to the version of the V8 JavaScript engine built into node
    * [`V8` docs](https://nodejs.org/dist/latest-v13.x/docs/api/v8.html#v8_v8)
* The `readline` module provides an interface for reading data from a readable stream such as the standard input (`stdin`) one line at a time. It makes working with incoming data easier than using `process.argv`
    * [`readline` docs](https://nodejs.org/dist/latest-v13.x/docs/api/readline.html#readline_readline)
    * When using the `readline` module you must specify an interface and pass it an object that specifies the input and output to use
    * To get data from the input and do something with it, use the `readlineInterface.question()` method which accepts data to be printed to the output first, and then a callback that handles the input
    * An example of a little program written with `readline`: 

    ```js 
    const readline = require('readline');

    // readline requires an interface
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout
    });

    rl.question("Do you think these notes are good? ", answer => {
        console.log(`Your answer: ${answer}`);
        process.exit();
    });
    ```
* The `module` module is a core module for working with modules (lol)
    * [`modules` docs](https://nodejs.org/dist/latest-v13.x/docs/api/modules.html#modules_modules)
    * Node.js works on the module pattern where each file is treated as a single module 
    * We can get modules by requiring them, but that's only half the pattern. We also have to export modules that will be used by other modules 
    * You can use `module.export` and set it equal to anything within your file of any data type to make it available in another module 
    * E.g: 
    ```js 
    function foo() {
        console.log("Hello world!");
    }

    module.exports = foo;
    ```
    * you could also export an object of functions, single functions, arrays, classes, etc
    * It is good practice to separate out reusable functions and classes into modules in order to maintain code readability, simplicity, and modularity
* The `events` module is a core module for working with events 
    * [`events` docs](https://nodejs.org/dist/latest-v13.x/docs/api/events.html#events_events)
    * Node is event-driven; much of it's core API is built around an an architecture in which event emitter objects emit named events that are listened for by event listeners that run callbacks. This is what makes node non-blocking. The `events` module is very important because all other node objects that are able to emit events are instances of the `EventEmitter` class in the `events` module
    * The `events` module allows you to create custom instances of the `EventEmitter` class that will emit a custom-named event, as well as specify listeners to handle that custom event
    * E.g:

    ```js
    // import module
    const { EventEmitter } = require('events');

    // create a new instance of the EventEmitter class
    const emitter = new EventEmitter();

    // specify the name of the event to emit and what to do with it
    emitter.on("customEvent", (message, user) => {
        console.log(`${user}:${message}`);
    });

    process.stdin.on('data', data => {
        const input = data.toString().trim();
        if (input.toLowerCase() === 'exit') {
            emitter.emit("customEvent", "bye!", "process");
            process.exit();
        }

        // raise an event and specify the data available to the handler
        emitter.emit("customEvent", input, "terminal");
    });
    ```
    * You may find it very useful to create your own events that fire at certain times so that you can run callbacks when those events are raised


## File system basics
* The `fs` module is used to interact with the file system 
* [File System docs](https://nodejs.org/dist/latest-v13.x/docs/api/fs.html)
* To read the files of a directory use `fs.readdirSync('path/to/dir')` and this will return an array of files in the directory 
* To list all the files in a directory, you could use: 
    ```js
    const fs = require('fs');
    const files = fs.readdirSync('./');
    files.forEach(file => console.log(file));
    ```
    * This will output the name of each file to the console 
    * There is also an asynchronous way to read files with `fs.readdir('path', callback(err, files));`
    * The callback specifies what to do with the file when it's read, and the callback must take an error argument to do something if there is an error 
    * E.g:
    ```js
    // asynchronous
    fs.readdir('./', (err, files) => {
        if (err) throw err;
        console.log(files);
    });
    ```
* `fs` can be used to read the contents of a file with the `fs.readFileSync('path/to/file', encoding)` method 
    * Often the encoding will be UTF-8
    * To read the contents of a file and output it to the terminal, use:
    ```js
    const file = fs.readFileSync('./example.txt', 'UTF-8');
    console.log(file);
    ```
    * This will output the contents of the file
    * The same can be done asynchronously:
    ```js
    // read file contents asynchronously
    fs.readFile('/path/to/file', encoding, (err, contents) => {
        if (err) throw err;
        //do something with contents when finished reading 
    });
    ```
    * This will read the file contents asynchronously and do whatever you specify with them when the method finishes
    * Binary files can also be read by not supplying the encoding argument. When a binary is read the output will be a buffer
* You can also create new files and write data to them with `fs.writeFile('/path/', content, callback);`
    * Write a simple markdown file:
    ```js 
    const text = `# This is a markdown file generated by Node.js`;
    fs.writeFile('./example.md', text, err => {
        if (err) throw err;
        console.log('File saved.');
    })
    ```
    * The method above asynchronously writes a file called 'example.md' and writes the supplied text to it, and saves it in the supplied directory
* You can create a directory with `fs.mkdir(path/to/name, {options}, err => {
    // handle err;
});`
    * This will asynchronously write the directory/directories
    * To write parent and child directories use `{ recursive: true }` for options 
    * You cannot write a directory that already exists, so to check if a directory exists, use `fs.existsSync(name)`:
    ```js 
    if (fs.existsSync('path/to/dir')) {
        console.log('This directory already exists.');
    } else {
        fs.mkdir('path/to/dir', options, err => {
            if (err) throw err;
            console.log('Directory created');
        });
    }
    ```
* To append data to an existing file use `fs.append(path/to/file, data, err => {
    // handle err;
});`
    * If the file you are attempting to append content to does not exist, the method will automatically make the file then write to it
* To rename a file synchronously, use `fs.renameSync(path/to/old/name, path/to/new/name);`
* To rename a file asynchronously use `fs.rename(path/to/old/name, path/to/new/name, err => {
  if (err) throw err;  
});`
    * The `fs.rename` and `fs.renameSync` methods can also be used to move a file to a new directory simply by providing a new path instead of a new name
* To delete a file use `fs.unlink(path/to/file);`
    * Like many other node APIs, the `fs.unlink` method also has an asynchronous option
    * To rename the methods above can also be used but instead pass them directory paths rather than file paths
    * To remove a directory, use `fs.rmdir(path, err => {
        if (err) throw err;
    });`
    * There is also a synchronous option that takes no callback
    * Before removing a directory, you must delete all files within it

## Files and streams

### Readable streams 
* The file system comes with a way to create readable streams 
    * the `fs.createReadStream(pathToFile, [encoding])`
    * You can then read data from a file through a stream, and this is less memory-intensive because the stream reads data chunk by chunk rather than all at once 
    * Streams raise the 'data' and 'end' events which are emitted when data comes through the steam, and when data is finished coming through the stream, respectively 
    * (note: common encoding is "UTF-8" for standard text)

### Writable file streams
* You can create streams that allow you to write to something other than the standard output, such as a file 
    * Use the `fs.createWriteStream(path/to/file, [encoding])`
    * You can then use the `write()` method to write data to that stream 
    * Readable and writable streams are meant to work together. Read data from one stream, write it to another 
    * There are a lot of methods for writing to streams 
        * E.g the `pipe(stream)` method allows you to "pipe" data from the standard input to a writable stream you created, such as a stream to a file 
        * The `pipe()` method can be called on `process.stdin` or a writable stream you create

### Child processes
* Node comes with a child process module that allows you to execute external processes in the Node environment 
* This means that node can interact with other applications 
* [`child_process` docs](https://nodejs.org/dist/latest-v13.x/docs/api/child_process.html#child_process_child_process)
* [Article about `child_process`](https://medium.com/the-guild/getting-to-know-nodes-child-process-module-8ed63038f3fa)
* E.g:

```js 
const childProcess = require('child_process');

// this will open the web browser and open the page specified
childProcess.exec('open http://www.linkedin.com/learning');
```
* The `child_process.exec()` method can be used to execute any node or external process
* the `child_process.spawn()` method is used for asynchronous processes
* For instance if you have a small node terminal program that runs asynchronously, to execute it as a child process you would use `spawn()`:

```js 
const childProcess = require('child_process');

// first arg is command, second is an array containing the file
const asyncApp = childProcess.spawn('node', ['asyncApp.js']);

// now you can write a handler to deal with the standard output from the process
asyncApp.stdout.on('data', data => {
    // do something with data 
});

// display a message when the process closes
asyncApp.on('close', () => {
    console.log('asyncApp process exited');
})
```

* You could also write data into the process:

```js 
asyncApp.stdin.write(/* data you want to input to the process */);
```

## HTTP servers
* To create an http server, require the `http` module and use the `createServer` method:

```js
const http = require('http');


http.createServer((request, response) => {
    response.writeHead(200, {'Content-Type': 'text/plain'}); // resp header
    response.end('Hello World\n'); // resp body/payload
})
.listen(1337, '127.0.0.1'); // listen on port
```
* In `.listen` the values above are default but not needed. You can listen on any port, on any ip. Leave out the ip argument if needed and it will use the ip of the device

## Getting help
* [Node.js documentation](https://nodejs.org/en/docs/)
