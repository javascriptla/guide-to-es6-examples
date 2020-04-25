# Chapter 2 - Functions

Welcome to Chapter 2!  In this chapter, we’ll continue to explore new syntax from ES6, starting with functions.  

## Introducing Arrow Functions in ES6

### Basic Syntax

```javascript
let z = (x, y) => x + y  
/*
  ES5 equivalent:
  var z = function(x, y){ return x + y; }
*/
console.log(z(1,2))
```

With fat arrow functions, the syntax on the left of the assignment operator `=` denotes the function’s parameters, `x` and `y` in the example.  The syntax on the right of the `=>` denotes the function body and its **implicit return value** (which is `x+y` here).  

> Note that we don't need curly brackets `{}` if our code after `=>` is on the same line.  In ES5, this code would translate to: `var z = function(x, y){ return x + y; }`.

### Single Parameter Arrow Function

```javascript
let greetName = name => `hello ${name}`   
/*
  ES5 equivalent
  var greetName = function(name) {  return "hello " + name;  }
*/
console.log(greetName('vijay'))
//hello vijay
```

In this example, `greetName` is a variable equal to a function with one parameter, `name`.  If we only have one parameter, we don’t need parentheses.   

### Multi-Line Function Body

```javascript
let greetAndPrintName = name => {   
  let str = `hello ${name}`;   
  console.log(str);
}

greetAndPrintName('vijay')
//hello vijay
```


### Multi-Line Return Value

```javascript
let result = (x,y) => (
  x+y
)
console.log(result(1,2)) //3
```



> ### Aside - Use of Semicolons in ES6
> You might have noticed by now, I haven’t really been using semicolons `;` a whole lot in my examples above with ES6.   This is because in ES6, they get added automatically by the compiler, where as in ES5, you needed to be very careful about your semicolons.
> While it’s nice to not have to type semicolons after every statement, it is still good practice to know how and when to use them (it helps add clarity to code and prevents your code from being prone to error and misinterpretation).

Now that we’ve had some time to look at functions, let’s move on to function parameters in ES6.  We’ll take a look at the default, rest and spread parameters.

## Default parameters

Sometimes you need default values for a function, especially if none are supplied during a function call.  Consider the following example in ES5.

```javascript
function add(num1, num2){
  return num1 + num2;
}  
var result = add(1);
console.log(result);
//NaN
```
This function returns `NaN` when we pass in only one argument.  What if we pass in no arguments?  How can we prevent our users from seeing unexpected or garbage return values?

### Default Values with ES5 syntax:

```javascript
function add(num1, num2){
  num1 = num1 || 0;   
  num2 = num2 || 0;   
  return num1 + num2;
}  
var result = add(1);
console.log(result);
//1
```


### Enter Default Parameters in ES6:

```javascript
const add = (num1=0, num2=0) => {   
  return num1 + num2;
}  
let result = add();
console.log(result);
//0
```

This code is much simpler. With default parameters, we can initialize our parameters (in this example `num1`, `num2`) to have default values (of `0` for the `add` function).  


## Rest Operator

### Using the Rest Operator

```javascript
let sum = (total=0, ...numbers) => {   
  for (let i in numbers) total += numbers[i];   
  console.log(total);
}  
sum(1,2,3,4,5,6,7)
//28
sum(1,2,3,4,5,6)
//21
sum(1,2,3)
//6
```

The rest operator is basically three dots `...` followed by the parameter name (in this case `numbers`), which then **creates and returns an array of all the extra parameters**.  

Since `numbers` is an array, we can use built-in methods like slice, pop, push, forEach to manipulate the array in our function body; something we couldn't do with the `arguments` keyword in ES5.


**Rewriting Our Example With `Array` Built in Method forEach:**

```javascript
let sum = (total=0, ...numbers) => {   
  numbers.forEach(num => total += num)   
  console.log(total);
}  
sum(1,2,3,4,5,6,7)
//28
```

Since the rest parameter allows us to input as many numbers as we want at the time of function call, we can basically create a cool little `sum` function that adds any number of parameters together.   

> ### Note:
> When using the rest operator, it must always be the last parameter in your function declaration.  If you add more parameters after the rest operator, you’ll get an error:

```javascript
let sum = (...numbers, firstname) => {
  //your code here
}
```

This code throws an error, rest parameters must always be last parameters in function declaration.

Next, let’s look at the spread operator.

## Spread Operator

Think of the spread operator as an alias for those finite set of values.

### Plugging In Values With Spread

```javascript
let sum = (a,b,c) => {   
  console.log(a + b + c)
}  
sum(...[1,2,3])
//6
```

Here we define `sum` to be a function that has 3 parameters, `a`, `b` and `c`.  Inside the function body, we simply `console.log` the sum of `a`, `b` and `c`.   Nothing fancy here, but notice the next line using the spread operator `...`.

Using the syntax: `...[1,2,3]`, we’re essentially saying that we want `1`, `2` and `3` to be the values of `a`, `b` and `c` for our `sum` function.   We get back the value `6`.

### Merging & Concatenating Arrays

```javascript
let a = [3,4,5]
let b = [1,2, ...a, 6, 7]  
console.log(b)  
//[1,2,3,4,5,6,7]
```

In ES5, to join two arrays together, we would have had to type code like this:

```javascript
var a = [3,4,5];  
var b = [1,2];  
var c = [6,7];  
b = b.concat(a);  
b = b.concat(c);  
console.log(b);
```
Much easier to do it in ES6 right?  Using the spread operator makes it simpler to create unions of arrays.  No more messy `concat` methods needed.

## String.includes() method

If you were searching a string in ES5 for a specific character or pattern, you’d most likely have to either use the `indexOf` method or a regex pattern to find a match.

In ES6, you can the new `String.includes()` method for fast searching.

**Example 1:**

```javascript
let search = `Jimmy Dean`  
let text = `Jimmy Dean was a good man`  
let text2 = `Jimmy dean was a good man`  
let text3 = `A good man was Jimmy Dean`  
console.log(text.includes(search))
//true
console.log(text2.includes(search))
//false
console.log(text3.includes(search))
//true
```

In this example, we have a variable search with value `Jimmy Dean`.  We want to check `text`, `text2` and `text3` if they include our search value.   Upon calling the `String.includes` method on each text, we get back `true`, `false` and `true`.   

`text` returns true because `Jimmy Dean` is found case-sensitive, while `text2`, `Jimmy Dean` is not found,  (**`String.includes` method is case sensitive**; hence `text2` returns `false`.   In `text3`, `Jimmy Dean` is found at the end of the string, so we get the value `true`.


```javascript
text.includes(search, 14)
//false
text.includes(search, 0)
//true
```

We can also tell our `String.includes` method where exactly to start our search by adding in the position we want to start as the second argument to the function.  This could prove handy if we know the item to be searched comes toward the end of a string, rather than at the beginning.

## Exercises:

- What’s the main difference between an arrow function and a function literal (from ES5)?
- Write a function purely in ES6 that calculates the weather in celsius from fahrenheit.  Make use of the new syntax you’ve learned throughout this book.  Focus on efficiency.
- Suppose you wanted to find out if certain values in one array were not in another array.  How would you go about finding the difference, given you’ve learned about the new spread operator?   Is there a built in Array method you can use to help you?
