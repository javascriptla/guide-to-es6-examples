# Chapter 3 - Objects & Classes

## Single notation

In ES6, objects can be written the following way:

```javascript
let age = 24;
let gender = "boy"  
let person = {
  age,
  gender
}
```

**Compare with ES5 syntax:**

```javascript
var person = {   
  age: 24,   
  gender: "boy"
};
```


> Note: the variables have to be previously defined to use the single notation syntax when creating a new object.   

## Destructuring Objects & Arrays

**Let’s first look at some syntax:**

```javascript
let { myVariable } = { myVariable: "green Salad"}

// store the property of myVariable in a let variable
console.log(myVariable);
```


**Another Example:**

```javascript
const { salad : mySalad } = { salad: "BBQ Chicken" }

// Here we look for a property in the object, salad,
// which gets saved in a new const variable called mySalad
console.log(mySalad)
//BBQ Chicken
```

This time we destructure an object on the right hand side with a property of salad and save it in a new variable called `mySalad`, denoted by `{ salad: mySalad }`.  Note the syntax as it's a little tricky.   

The first property on the left represents the property to be searched on an object to be destructured, and the property to the right of the `:` is the new variable, `mySalad` which gets created after destructuring.   

**In summary when creating your own variables using destructuring:**

`let { propertyToBeSearched : newVariableName } = { propertyToBeSearched : someValue }`

### More Examples

Consider:

```javascript
let giveMeADrink = () => {
  return {
    drink: "coffee",
    temp: "hot",
    size: "small"
  }
}
let { drink } = giveMeADrink()

//We destructure the drink property from the object
//returned by giveMeADrink()
console.log(`Today I'm having a ${drink}`)
```

In this example, we have a simple function that returns an object.  But we only need the `drink` property, so we just *destructure* the object and use it as a variable for our `console.log` statement.  

What if our property is inside another object in our initial object?  How do we access the property then?  Consider:

```javascript
let giveMeADrink = () => {
  return {
    beverage: "coffee",
    temp: "hot",
    sizes: {
      small: "tall",
      medium: "grande",
      large: "venti"
    }
  }
}

let {
  beverage: drink,
  sizes: {
    medium: size
  }
} = giveMeADrink()

console.log(`Today I'm having a ${drink} and it's a ${size} size.`)
```


Alternatively, if you wanted to save some time and just use the same variable names as the properties, you could write your code this way:

```javascript
let { beverage, sizes:{ medium }  } = giveMeADrink()

//translates to
let beverage = "coffee"
let medium = "grande"
```


### Destructuring also works for arrays as well:

```javascript
let [a,b] = [3,(x,y)=>x+y, 3]

//Translates to:

let a = 3
let b = function(x,y) {
  return x + y
}

console.log(a, b(a,a))      
// 3 6
```


## Classes


**Here’s the basic syntax of a class:**

```javascript
class MyClass {
  someFunc() {
    console.log('Hello World')
  }
}

//Here's ES5 constructor syntax to compare:  
function MyClass() {
  this.someFunc = function() {
    console.log('Hello World');
  };
}

const myObj = new MyClass() myObj.someFunc()
```


**Here’s another example:**

```javascript
class Mammal {
  constructor(name) {
    this.name = name;
  }
  walk() {
    console.log(`${this.name} is walking.`)
  }
}
```

**Here’s the old ES5 code for comparison:**

```javascript
function Mammal(name) {
  this.name = name;
  this.walk = function() {
    console.log(this.name + " is walking");
  }
}

var monkey = new Mammal("Jim");
monkey.walk()
```

## Inheritance


### `extends`


```javascript
class Mammal {
  constructor(name) {
    this.name = name;
  }
  walk() {
    console.log(`${this.name} is walking.`)
  }
}
class Person extends Mammal {
  //here's where we'd put our default parameters,
  //probably if we liked cloning people   
  constructor(name = "Jango Fatt", gender = "male", age = 30) {
    super()
    this.name = name;
    this.gender = gender;
    this.age = age;
    this.init()
  }
  signIn() {
    console.log(`${this.name}, ${this.gender}, ${this.age} signed in.`)
  }
  init() {
    this.signIn();
    super.walk();
  }
}
let p = new Person();
//Jango Fatt, male, 30 signed in.
//Jango Fatt is walking.
```

## More Keywords Related To Classes

### get and set

Now with ES6 classes, you’re bound to see `get` and `set` a lot more.  Here’s a few examples to see how they work.

```javascript
class Person {
  constructor(name) {
    this._name = name
  }
  get name() { //get the name     
    if (this._name) {
      console.log(this._name)
    } else {
      console.log("No name yet, how about you set one?")
    }
  }
  set name(changeName) {
    //change the name  this._name = changeName;     
    console.log(`${this._name} is now the new person's name`)
  }
}

let baby = new Person();

console.log(baby.name)
//No name set yet, how about you set one?  
baby.name = "Alice"
//Alice is now the new person's name
```



### this in ES6

In ES6, `this` still follows the basic rules of ES5; it is used to provide the context of a code block or function or object.

One issue that always plagued ES5 programmers was that if they created a new object with a constructor, any functions defined would need to be passed a copy of `this` in order to get the correct context as opposed to something else (like `this` being equal to an element on the DOM or a `global` object like `window`).

**Here’s an interesting example:**

```javascript
function myFunction() {
  console.log(this)
}
let myObj = {
  hello: function() {
    console.log(this);
    myFunction();
  }
}
myObj.hello()

/*
Object { hello: hello() } -- comes from myObj
window -- comes from myFunction which is attached to the window object
*/
```

Though we call `myObj.hello()`, the value of `this` is different in each case?  Why?

It’s because `this` defined inside a function has a different scope than `this` defined in an object!  Hence, if we want to fix this issue, we need to use the JavaScript `bind` function on `myFunction` which allows us to set `this` to the `object`'s scope, not the `window`'s scope.

```javascript
function myFunction() {
  console.log(this)
}
let myObj = {
  hello: function() {
    console.log(this);
    let myFunctionBinded = myFunction.bind(this);
    myFunctionBinded();
  }
}
myObj.hello()
//Object { hello: hello() } - myObj.hello
//Object { hello: hello() } - myFunctionBinded
```



**Another example**

```javascript
function Mammal(name) {
  this.animalName = name || "Henry";
  this.sayName = function() {
    setTimeout(function() {
      console.log("My name is " + this.animalName);
    }, 200);
  }
}
var dog = new Mammal();
dog.sayName();
//My name is undefined
```


**Old ES5 fix: Copy `this` into another variable, like `that` (or `self`).**

```javascript
function Mammal(name) {
  this.animalName = name || "Henry";
  this.sayName = function() {
    var that = this;
    setTimeout(function() {
      console.log("My name is " + that.animalName);
    }, 200);
  }
}
var dog = new Mammal();
dog.sayName(); //My name is Henry
```


### Introducing Lexical `this` with Fat Arrow Functions


```javascript
class Mammal {
  constructor(name = "Henry") {
    this.animalName = name;
  }
  sayName() {
    setTimeout(() => {
      console.log(`My name is ${this.animalName}`)
    }, 200)
  }
}
let dog = new Mammal();
dog.sayName();
//My name is Henry
```

### Overrides

Before we call it a chapter, let’s look at one more thing we can do with classes in ES6, and that is **overriding** parent methods; basically changing what should happen to a function inherited from a parent `class` in a child `class`.

**For example:**

```javascript
class Animal {
  constructor(name = "Henry", sound = "lol") {
    this.name = name this.sound = sound
  }
  speak() {
    console.log(`${this.name} says ${this.sound}`)
  }
}
```
So far so good.  Let’s now extend the parent `Animal` class into a new child `class` called `Dog`.

```javascript
class Dog extends Animal {
  constructor(name, sound) {
    super(name, sound)
  }
  speak() {
    console.log(`${this.name} says bow wow wow!`);
  }
}
let fido = new Dog("Fido") fido.speak()
//Fido says bow wow wow!
```

We’ve **overridden** the functionality of the speak method in the `Dog` class.  Instead of using the `Animal` method `speak` which `console.log`s `${this.name} says ${this.sound}`, the `Dog` method `speak` `console.log`s `${this.name} says bow wow wow!`.


```javascript
class Dog extends Animal {
  constructor(name, sound) {
    super(name, sound)
  }
  speak() {
    console.log(`${this.name} says bow wow wow!`);
    super.speak();
  }
}
let fido = new Dog("Fido") fido.speak()
//Fido says bow wow wow!
//Fido says lol
```

## Exercises

- Why do you think desctructuring is useful when working with objects and classes in JavaScript?
- What does inheritance mean in relation to JavaScript object oriented programming?
- What does the `this` keyword mean? What are some issues with the `this` keyword in ES5?
- What does the `super` method do?
- What does the `constructor` function do in a `class`?
- What does it mean when someone says they want to override a class method?
- How do we use `get` and `set` with an ES6 class?  Write out your own example.
