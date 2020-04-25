# Chapter 8 - Keeping Up With ES6, 7, to the Moon!


### What’s a Transpiler?

A transpiler with regards to using JavaScript is a script / program that allows you to turn your shiny new EcmaScript code into code that is browser compatible.

#### Using Babel in the Browser

Let’s preview what we can do with a transpiler called [Babeljs.io](https://babeljs.io/).

> Note: You’ll always want to refer to the official documentation as things do change over time.   At the time of writing Babel is in its 7th iteration.

If you’re lazy and just want to use the least amount of tools available to get your code to work, you’ll probably want to just create a simple `HTML` file, and then download the script from Babel to add to your webpage, so that you can try out some ES code afterwards.

**Working in the browser with Babel:**

```html
<div id="output"></div>

<!-- Load Babel -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<!-- Your custom script here -->
<script type="text/babel">
  const getMessage = () => "Hello World"; document.getElementById('output').innerHTML = getMessage();
</script>
```

First off, take this code and create a `HTML` file, then paste it in there.  Next, open the file in a browser and you should see the message `"Hello World"`.

In this example, we have a simple `HTML div` with an `id` of `output`.  We next load the Babel transpiler script into our page via a JavaScript `<script>` tag (as you would always do whenever loading in another JavaScript library or file).

Finally, and this is the part you’ll need to pay most attention to -- we create another script for our ES code that is of `type="text/babel"` inside the script tag.   

>(If you don’t do this, your code will not get transpiled -- instead the browser will be unable to process the code if it doesn’t support it).   Babel takes your ES code and translates it into a final script tag that is ES5 (which all browsers currently support and can run).   Go ahead and try out some of the examples we’ve done in this book so far if you want to.

**Another example if you want to `console.log` stuff:**

```html
<!-- Load Babel -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<!-- Your custom script here -->
<script type="text/babel">
  let p1 = new Promise((res, rej) => {
    setTimeout(() => {
      res(12)
    }, 3000)
  })
  let p2 = new Promise((res, rej) => {
    setTimeout(() => {
      res(23)
    }, 1000)
  })
  let p3 = new Promise((res, rej) => {
    setTimeout(() => {
      res(43)
    }, 5000)
  })
  Promise.race([p1, p2, p3]).then((values) => {
    console.log(values) //23
  })
</script>
```

#### Using Babel with Node.js (Server Side)


If nothing else, be sure to first download and install [Node.js](https://nodejs.org/en/) on your computer.  These commands will work on both Windows and Mac, however if you use Windows, you'll need to run these commands after installing [Git Bash](https://gitforwindows.org/) on your Command Prompt, which will allow your Windows computer to use UNIX commands.  

**Using `export` and `import` with Babel Transpiler**

For this example, let's go ahead and create a sample `test` directory to practice using Babel.  Navigate to a projects folder (such as your User directory, I'm using `/Users/myName/Sites/tests` on my Mac) with your console, and then type in the following.

```console
mkdir test
cd test
npm init -y
touch Car.js
touch index.js
```

The command `npm init -y` creates a `package.json` file with default settings that Node.js and its built in NPM (Node Package Manager) will understand, so if we want to use Babel with our project, we can add it to the `package.json` file and NPM will download it for us (we don't need to go to the web ourselves and download the files).  

Note, we've also created two files, `Car.js` and `index.js` which will be our *test modules* for practicing ES6 `import` and `export`.

**Let's go ahead and download Babel.js using NPM**

```console
npm install --save-dev @babel/core @babel/cli
```

> Note: Directions can always change so be sure to reference the BabelJS website for any discrepancies here. [Installation of Babel with NPM](https://babeljs.io/setup#installation).

Be sure to open your project folder with your favorite editor, like Atom or Sublime Text, and look at all the files created.  You should see this structure in your directory.

```txt
| test
  | node_modules
  - Car.js
  - index.js
  - package.json
```

Come back to your own project folder now and let's look at the `package.json` at the root directory of the `test` folder.  Your `package.json` should now look like this:

**Updated `package.json`**

```js
{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/cli": "^7.4.4",
    "@babel/core": "^7.4.4"
  }
}
```

Note the new entries for Babel (note: your Babel version numbers may vary).  

```js
"devDependencies":{  
  "@babel/cli": "^7.4.4",
  "@babel/core": "^7.4.4"
}
```


**In your test folder, run the following:**

```console
touch .babelrc
npm install @babel/preset-env --save-dev
```

**In your `.babelrc` file (not command line), add the following**

```js
{
  "presets": ["@babel/preset-env"]
}
```
Great!  You've successfully downloaded and installed Babel with your project folder.  You can now start transpiling from the command line.  Let's write some ES6 now.

**Go to your `Car.js` file and add the following code:**

```js
let randomCar = () => {
  let carNames = ["Toyota", "Mercedes", "Audi", "Honda", "BMW", "Tesla", "Volkswagon"]
  let rand = (Math.floor(Math.random() * Math.floor(carNames.length)))
  return carNames[rand]
}

function driveMyCar(name) {
  return `Driving a ${name}`
}

export { randomCar, driveMyCar }
```

**Go to your `index.js` file and add the following code:**

```js
import { randomCar, driveMyCar } from "./Car"

let name = randomCar()
console.log(driveMyCar(name)) //Driving a Toyota
```

Great, now let's convert your ES6 code to ES5 code with Babel.

**From the command line:**

```console
./node_modules/.bin/babel index.js -o index_es5.js
./node_modules/.bin/babel Car.js -o Car_es5.js
```

You should see the new index_es5.js and Car_es5.js files generated in your project folder.  Open both of them up to see the ES6 code you wrote now transpiled to ES5 code.

Next, we'll need to make a small change to the index_es5.js file.  Look for the line that says

```js
var _Car = require("./Car");
```
and change that path to your Car_es5.js like so:

```js
var _Car = require("./Car_es5");
```

This tells the index_es5.js file to use the Car_es5.js file to get the Car definitions, not the index.js file with ES6 definitions.

**You can now test the code by running the following in your console:**

```console
node index_es5.js
```

**You should see the following output:**

```console
Driving a Tesla
```
>Note your car might vary -- it is random after all!

If you have trouble with running the ES5 code after it's been transpiled, and are getting an error that says something along the lines of `Unexpected token exports`, be sure you are using `Car_es5.js` in your `index_es5.js`'s `require` statement as shown above.

**Making the Process Smoother**

If manually transpiling each of your files is a chore, we can speed up our workflow by adding a few scripts inside our `package.json` file so Babel feels smoother.  

>Note: There's a ton of little customization features for Node, we're really just scratching the surface for today.

**Adding scripts to our `package.json`**

```js
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "babel": "./node_modules/.bin/babel"
},
```
Under the scripts section after test, add the line `"babel": "./node_modules/.bin/babel"`.  Now when you are in the console, you can run the command:

```console
npm run babel index.js -o index_es5.js
```

Feel free to swap whatever file you want here with `index.js` and `index_es5.js`. You no longer need to constantly type out `./node_modules/.bin/babel` anymore, this entry in the scripts object saves this path for you so you can just type `babel` instead with `npm run`.

We can also save ourselves the hassle of having to output files as `_es5` files, by moving all our transpiled files into a folder for ES5 instead.  Then we don't need to manually change paths in our transpiled files like we had to do above with `index_es5.js` and its `require` statement.

**Let's make two new directories first in our test folder:**

```console
mkdir lib
mkdir src
```

Next, move your `index.js` and `Car.js` files into the `src` folder. You can easily do this with the command line:

```console
mv index.js src
mv Car.js src
```
This command moves both files into the new src folder for safekeeping.  We'll put all future ES6 files here.  We will transpile all these files and output them to the `lib` folder.  When we use our code with `HTML` in a `script` tag, we'll want to reference the `lib` folder as it has compatible ES5 code.

Now let's go to our `package.json` file once more. Are you starting to love `package.json`?

**Add the following under `scripts`**

```js
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "babel": "./node_modules/.bin/babel",
  "build": "babel src -d lib"
},
```

The new line here is `build`, which runs `babel` on our `src` folder and outputs each transpiled file to our `lib` folder using the flag `-d` for directory.  Let's try it out.

**Run the following command**

```console
npm run build
```

Now navigate to your `lib` folder with the console.

```console
cd lib
```

Finally run index.js with the node command to see your transpiled code do its magic!

```console
node index.js
```


#### Trying Out New ES Features

**Command Line Syntax**

*Step 1: Create your project folder and `npm init`*

```console
mkdir myExample && cd myExample;
mkdir src npm init -y
npm install --save-dev @babel/core @babel/cli
npm install --save-dev @babel/plugin-proposal-object-rest-spread
```

Make sure your `package.json` looks like this.  Be sure to include the `scripts` lines (below), which tells Babel to build your code from the folder `src` (we’ll create it next), whenever we type the command `build`.  It’s okay if the version numbers are slightly higher than what you see here.

```js
{
  "name": "myExample",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "babel src -d lib"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/cli": "^7.2.3",
    "@babel/core": "^7.2.2",
    "@babel/plugin-proposal-object-rest-spread": "^7.2.0"
  }
}
```

*Step 2: Create a `.babelrc` file*

```console
touch .babelrc
```

```js
{
  "plugins": ["@babel/plugin-proposal-async-generator-functions"]
}
```
Add this line inside the `.babelrc`:

*Step 3: Create your script file inside a folder called `src`, where you can try out some ES8.*

```console
touch src/index.js
```

```js
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };

console.log(x); // 1
console.log(y); // 2
console.log(z); // { a: 3, b: 4 }
```

![img](https://lh4.googleusercontent.com/Y-VVc3mlP2PJU-g6LUeP_35VC8sFVDCcq9gKdltFgFHr2nbq0yMUBlBo4DR3pzQnzO3LbW7eJVI8CGT8R7frj85QJu6Cq44X321nbHzTK1T9BUMPWGgOnKVGvjt1jUxaB9AgSLrX)

This is the new ES8 code that lets you use the spread operator inside an object.  As you can see from the example, `z` is destructured from the object on the right hand side of the equation to give back a new object with properties and values for `a`, `b`.

To run the code, simply execute the following command in your terminal:

`npm run build`

You should now see your transpiled output (in ES5) in the newly created folder lib, under `index.js`.

![img](https://lh3.googleusercontent.com/MQPncnJigzsJuUQ_ZAqRAVB_4HqBKspRDnFWTsWW6Ed_N5qFEo9yWCsR0zok7byu6m2-IYwQECJ1_TTCA9oXMegfIhhIplfBqykfQS1Xyx4Ffc8obwCNqGgKMELLm6PmRPXBXhWV)

Transpiled ES5 Code from our ES8 Code.  Isn’t it beautiful?

To execute the file, simply run:

`node lib/index.js`

This will give you the output as intended by our example.

![img](https://lh3.googleusercontent.com/w48e2jdVg5vEOt2Of6xXq4yMhTUoJzWCjlYm2HjQoMThmmMBM_QzZSj2r4AIGX6ZjTh936A30gErTEVGbiL_0T5dTlIWwAjp5YFXSktH48Yx2i1Ked0J-25OCNBlibQCZVvp_2gN)

Nice! It worked!

If it doesn’t work, please be sure to verify the documentation (as it does change periodically) via: <https://babeljs.io/setup>.


## Exercises

- How often does EcmaScript get updated?
- Where can you find some of the new features of ES2016, 2017 and 2018?
- What is a transpiler, and what is it used for?
- What does BabelJS.io do?
- Are there other sites you can use if you don’t want to transpile?
