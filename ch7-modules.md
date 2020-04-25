# Chapter 7 - JavaScript Modules


## Modules in ES5 (Review)

Let’s pretend we are writing our own library, `Q` for fun.  

**We’ll start off with the following:**

```js
//new Q library
var Q = (function() {
  return {
    log: function(query) {
      console.log(query)
    },
    ver: 1.0
  };
}())

Q.log('hello'); //hello
Q.ver; //1.0
```


```js
//version 2 update
(function(lib) {
  if (lib.ver < 2) {
    update(lib);
  } else {
    console.log("Library at latest version")
    return lib;
  }

  function update(lib) {
    lib.add = function(num1, num2) {
      lib.log(num1 + num2);
    }
    lib.subtract = function(num1, num2) {
      lib.log(num1 - num2);
    }
    lib.ver = 2.0
  }
  return lib;
})(Q);
```

**Optional Plugin for Q library**

```js
//optional module (doesn't change library version)
(function randomNumGenerator(lib) {
  //random number from 0 to limit  
  lib.randomNum = function(limit) {
    Q.log(Math.floor(Math.random() * Math.floor(limit)));
  }
}(Q))

Q.ver; //2
Q.add(1, 1) //2
Q.randomNum(10) //any number 1-10
```


```html    
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
  <meta charset="utf-8">
  <title></title>
</head>

<body>
  <script type="text/javascript" src="Q.js"></script>
  <script type="text/javascript" src="Q.extend.js"></script>
  <script type="text/javascript" src="Q.optional.js"></script>
</body>
</html>
```

Believe it or not, this was the old way of writing a library and sharing it with others; you'd write your own JavaScript file containing your library, then make separate scripts for plugins, then finally add them altogether into script files on an HTML page.   As you can see this led to a huge issue with variables often breaking because other variables were using the same name!

Another issue, as you might have seen is that our successive modules don’t necessarily have access to any private variables (variables defined inside IIFEs) that were defined in other versions of Q, so we might have to recreate them again.

Loading all these scripts one by one causes great room for error. Imagine if we had dozens of scripts to load in manually? What if we made an error, or forgot the file? Hence, this way of writing JavaScript modules isn’t great for modern web development (it’s too slow, ugly, manual, tedious and at risk of code conflict).

## Enter Module Dependency Management (Exploring AMD & CommonJS syntax)

### AMD (Asynchronous Module Development)

RequireJS works by loading each module as defined by the developer along with the required dependencies before executing code.  **Only when all modules are loaded by RequireJS does it let JavaScript begin running on the webpage.**

**Let’s look at an example to help understand RequireJS better:**

First we have a simple project folder structure as defined below:

```txt
project folder >
  | js   
    - main.js  
    - require.js
  | lib     
    - jquery.js  
  index.html
```

```html
<html>
<head>
  <title>My Sample Project</title>
  <!-- data-main attribute tells require.js to load scripts/main.js after require.js loads. -->
  <script data-main="scripts/main" src="scripts/require.js"></script>
</head>
```

```js
/* main.js */
define(["lib/Q"], function(Q){   
  Q.log("hello")
})
```


**Q.js, where we stick our definition and dependencies for Q lib**

```js
define(["jquery"], function($) {
  return {
    log: function(val) {
      $("body").html(val)
    },
    ver: 1.0
  }
});
```


### CommonJS Syntax


**Let’s look at an example:**

```js
/***** q.js File *****/
function log(query) {
  console.log(query)
}
var version = 1.0;

module.exports = {
  log: log,
  ver: version
};

/***** app.js File *****/
var Q = require("Q.js");
console.log(Q.ver) //1.0
```

Writing in the CommonJS syntax is easy, and you can see why many developers adopted the pattern.   Given that Node.js also took off in the last several years, CommonJS has become the main standard for module management.    

It’s very easy to jump into a project using the CommonJS syntax and browse through different JavaScript files, know quickly how each file required relates to another file, and choose which files we want to require depending on our application logic (hence onboarding new developers into a web project is also easy).

### Universal Module Development (UMD)

**Here’s a quick peek at some code:**

```js
(function(root, factory) {
  if (typeof define === 'function' && define.amd) {
    // AMD. Register as an anonymous module.     
    define(['Q'], factory);
  } else if (typeof exports === 'object') {
    // CommonJS     
    module.exports = factory(require('Q'));
  } else {
    // Browser globals
    root.Q = factory(root.Q);
  }
}(this, function(Q) {
  Q.add = function(num1, num2) {
    Q.log(num1 + num2);
  };
  return Q;
}));
```

Take all the time you need to review this code block and our previous examples that led us here.  JavaScript modular development is a bit overwhelming, but if you're able to get the gist of the different ways of doing modular development (AMD, CommonJS, UMD) -- you're okay to continue reading.

## ES6 Modules

If you like the CommonJS syntax, you’ll be pleased to find that ES6 adopts many of the same patterns; modules are scoped to each file, independent of other modules.   There is also support for asynchronous module loading as well, if you’re a fan of AMD.

> Note: Browsers (and your current version of Node.js) may or may not support many of the features discussed in this section; so if you want to be able to test them out, you’ll have to use a transpiler program like [Babel](https://babeljs.io/) or [Traceur](https://github.com/google/traceur-compiler) to convert ES6 code into ES5 code your browser can understand.  Skip to Chapter 8, and see the section on transpiling with Babel via Node.js, and then come back here to view this code.

### export and import

`export` is akin to `modules.export` and `import` for `require` as we had seen above with CommonJS syntax.   

**Let’s take a look at how we’d use `export` and `import`**

```js
/*** Car.js File***/
let car = "Toyota"

export function driveMyCar() {
  return `${car} is driving`
}

/*** index.js File***/
import driveMyCar from "Car"

let myCar = driveMyCar()
console.log(myCar)
```


```js
import drive from "Car"

let myCar = drive()
console.log(myCar)
```


### Destructuring

**Let’s review some more code:**

```js
/*** Car.js **/
let randomCar = () => {
  let carNames = ["Toyota", "Mercedes", "Audi", "Honda", "BMW", "Tesla", "Volkswagon"]
  let rand = (Math.floor(Math.random() * Math.floor(carNames.length)))
  return carNames[rand]
}

function driveMyCar(name) {
  return `Driving a ${name}`
}

export { randomCar, driveMyCar }

/*** index.js ***/
import { randomCar, driveMyCar } from "./Car"

let name = randomCar()
console.log(driveMyCar(name)) //Driving a Toyota
```


```js
//we don't need the entire library
import { randomCar } from "./Car"

let name = randomCar()  
console.log(name) //Honda
```

**You can rename your imports in the syntax**

```js
import { randomCar as r } from "./Car";
```

This code snippet renames randomCar as the variable r

### `*`,  `default`, `as`  

We can also `import` everything from a library with the `*` syntax into a new object representing our Car.js file.   

```js
//If we want the whole library, we can use the * symbol  
//and give it an alias like Car for use in our code
import * as Car from "./Car"

let name = Car.randomCar()
console.log(Car.driveMyCar(name)) //Driving a Toyota
```

The `as` syntax allows us to rename an import (alias) as another variable name. In the above example, `import * as Car from "./Car"` is like saying get the whole library via `*` and put it into the `Car` variable.

The `default` syntax specifies what will be the **default binding** for the exported module file; in many cases you will see this notation when files are exporting entire ES6 classes.   

We can then `import` that entire `class` as a single object in another file. We can still additionally `export` extra functions and variables, but by default, the main exported value will be the `class` in this example.  

```js
/* Animal.js */
export default class Animal {
  constructor(name = "Harry") {
    this.name = name
  }
  sayName() {
    console.log(this.name)
  }
}

/* main.js */
import a from "./Animal";
```

Because we made a `default` binding with our `export` statement; when we import from the file `Animal.js`, by default it is the `Animal class`, even if we name it something else like `a` here.

```js
//Hello.js
function goodBye() {
  console.log("good bye!")
}

export default function helloWorld() {
  console.log('hello world')
}

export {
  goodBye
}

//main.js
import h, { goodBye } from "./Hello"
```

The syntax takes a little getting used to, but once you nail down the examples and get practice, you should feel pretty good about using `import`, `export` statements along with `default` statements and aliases.  

### Script type module

If you want to try using ES6 modules in the browser AMD style as opposed to server-side CommonJS style (as we’ve been doing above), you can use the following syntax (provided your browser supports it).

```html
<!-- In your HTML file -->
<script type="module">
  import {driveMyCar} from './Car.mjs';
  driveMyCar("Mazerati");
</script>
```

```js
//Car.mjs
export function driveMyCar(name) {     
  return `Driving a ${name}`
}
```

The `<script type="module">` tag allows us to use ES6 `import` statements to `import` modules from module files, denoted by the extension `.mjs`.   You can still also use `.js` as an extension, but it’s becoming good practice to denote module files as `.mjs` files rather than `.js` files especially when working with modules in the browser.

```html
<script type="module" src="Car.mjs"></script>
<script nomodule src="es5-Car.js"></script>
```

If the browser doesn’t support the `<script type="module">` tag, you can use a script tag with the `nomodule` attribute to be executed instead (hence it’s always a good idea to have backup code in ES5 when working with new ES6 syntax).

## Exercises

- What were some disadvantages of coding modules in ES5 before AMD and CommonJS?
- What is the difference between AMD and CommonJS?
- What is the `data-main` attribute from Require.JS and how does it work?
- What does `module.exports` mean?
- What is the difference between `import` and `export` statements in ES6?
- What does the `default` keyword mean in ES6 for a module?
- What is UMD, and what does it try to solve?
- Can we use ES6 syntax in the browser, and if so how?
