# Chapter 1 - Variables, Template Strings, Loops

## let

The `let` keyword is a new way to define a variable in ES6.  It introduces the idea of **block scope**, which is something ES5 did not have.

```javascript
let x = "Hello"

if(x == "Hello"){
  let y = " World"
}

console.log(x + y)
//Uncaught ReferenceError: y is not defined
```

#### To fix our example (one way to do it):

```javascript
let x = "Hello"

if(x == "Hello") {
  let y = " World"
  console.log(x + y)
}
```

### Naming Issues Solved with `let`

#### Consider the following example using ES5 syntax:

```javascript
var $ = function jQuery(){
  //do stuff
}
/*** adding in other libraries into our JavaScript  ***/

var $ = function angular(){
  //do other stuff
}

$("body").html("Hello World");
//Hello World didn't work! But why?  No error was thrown!
```

#### Let’s now look at this example using ES6 syntax

```javascript
let $ = function jQuery(){
  //do jquery stuff
}

/*** adding in other libraries into our JavaScript  ***/

let $ = function angular(){
  //do angular stuff
}

$("body").html("Hello World");
// SyntaxError: Identifier '$' has already been declared
```

**We can fix our error by renaming one of our libraries to be a different variable:**

```javascript
let $ = function jQuery(){
  //do stuff
}

let ng = function angular(){
  //do angular stuff
}
```

## const

```javascript
const API_KEY = '123456'

API_KEY = '654321';

//TypeError: Assignment to a constant variable.
```


#### Revisiting our example using `let` now with `const`

```javascript
const $ = function jQuery(){
  //do jquery stuff
}

/*** adding in other libraries into our JavaScript  ***/

$ = function angular(){
  //do angular stuff
}

$("body").html("Hello World");
// TypeError: Assignment to constant variable.
```


## Template Strings

```javascript
let x = 10, y = 20, a = 40, b = 15

let result = `Robert has ${x} apples,
${y} bananas, ${a} mangoes, ${b} ice creams`

console.log(result);
```

#### But Wait, There’s More! Template String Math


```javascript
let x = 12

let y = 42

console.log(`${x} + ${y} = ${x+y}`) //12 + 42 = 54
```

We can also calculate results of function calls inside template strings like below.

```javascript
let factorial = function(n){
  if(n==0){ return 1 }
  return n * factorial(n-1)
}

let result = `The factorial of 5 is ${factorial(5)}`

console.log(result) //"The factorial of 5 is 120"
```

## Multiline Template Strings


**ES5 Syntax:**

```javascript
var s1 = "Hello \n" + "World";
//Hello World

console.log(s1);
//Hello  World
```

**ES6 Syntax:**

```javascript
let s2 = `Hello World`

console.log(s2)

//Hello  World
```

No more need to add the `\n` character to separate new lines

## Tagged Templates


```javascript
let parseTemplate = function(text, variable1, variable2){
  console.log(text);
  console.log(variable1);
  console.log(variable2);
  return variable2;
}

let item1 = "Laundry"
let item2 = function(){ return "Groceries" }
let result = parseTemplate`I have to do my ${item1} and ${item2}`

//Array(3) [ "I have to do my ", " and ", "" ]
//Laundry
//function item2()

console.log(`Finished with ${result()}`)

//Finished with Groceries
```


```javascript
let styleTemplate = function(template, style){
  const d = new Date()
  const year = d.getFullYear()
  if(year == 2019){
    style = "red";
  }else {
    style = "green";
  }
  return template[0] + style + template[1];
}

let color = "";
let html = styleTemplate`<div style='color:${color};'>Hello World</div>`;

console.log(html);
//<div style='color:red;'>Hello World</div>
```



## Loops

### for...of Loop


**Consider the following code:**

```javascript
let list = [1,2,3,5]
let total = 0;
for (let i of list) {
  //iterates over every value in our array
  total += i;
}
console.log(total); //11
```

### for...in Loop (Review)


**Example:**

```javascript
  let myObject = {
    "name":"JavaScriptLA",
    "members":"3900",
    "youtube": "https://youtube.com/javascriptla",
    "instagram": "https://instagram.com/javascriptninja"
  }
  for(let i in myObject){
    console.log(`key: ${i}: , value: ${myObject[i]}`)
  }
```



## Exercises

- When do you think it would be better to use the `var` keyword in your code?  Is there any situation where you think the `var` keyword is probably a better choice?
- What happens if you define an array to be a `const`?   Can you keep adding values to the array with built in JavaScript methods like `Array.push` and `Array.pop`?
- What is a template string, and how is it used in ES6?
- Can you remember what the `for...of` loop does?  How about `for...in`?
