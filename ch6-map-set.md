# Chapter 6 - Proxies, Maps, WeakMaps, Sets, WeakSets

## Proxies

**Here is the basic syntax for a `Proxy`**

```js
let px = new Proxy(targetObject, {
  get: function(obj, prop) {
    // called when getting a property (prop) on our object (obj)  
  },
  set: function(obj, prop, value) {
    // called when setting a property (prop) on our object (obj)
  }
})
```

**Here’s a silly example:**

```js
let ourPeople = {
  theQueen: "The Queen",
  theHorse: "The Horse"
};

let canIDate = new Proxy(ourPeople, {
  get: function(obj, prop) {
    let person = (prop in obj ? obj[prop] : "Someone Else")
    if (person == "The Queen") {
      return `Sorry you can't date: ${person}`
    } else {
      return `Sure you can date: ${person}`
    }
  }
})

canIDate.theQueen;
canIDate.theHorse;
canIDate.someoneElse;
```

*Hey, do you think you can just go up and ask to date The Queen??*   

Let’s look at another example with proxies, one that could be used in a real world scenario.

```js
let trackMyWeight = () => {
  let defaultHealthData = {
    caloriesBurned: 0,
    stepsWalked: 0,
    height: 182, //cm     
    weight: 93 //kg   
  }
  let stepsWalkedToCals = steps => {
    return steps / 100;
  }
  return new Proxy(defaultHealthData, {
    get: function(obj, prop) {
      console.log(`Request received to get ${prop}`)
      return obj[prop];
    },
    set: function(obj, prop, value) {
      console.log(`Request received to set ${prop}, set to ${value}`)
      if (prop == "stepsWalked") {
        console.info(stepsWalkedToCals(value) + " calories burned")
      }
      obj[prop] = value;
    }
  });
}
let myHealthData = trackMyWeight();
myHealthData.stepsWalked = 10000
```

## Maps

- Maps can quickly be used to find their respective size (or length), using the `size` method.   
- Maps can have key values of **ANY** type, as opposed to strings and symbols only for Objects
- Maps are easy to iterate over using a built-in `forEach` method, while Objects have to be manually iterated

**Let’s look at a quick example of using a Map:**

```js
let myMap = new Map();

myMap.set('John', 'Cheeseburger');
myMap.set('Vijay', 'Chicken Tikka Masala')
myMap.set('Danielle', 'Vegan Lasagna')
myMap.set('function doNothing(){}', 'Did nothing')
myMap.set(NaN, 'yup NaN works too')

console.log(myMap.size); //5   

myMap.forEach((val, key) => console.log(`${key}: ${val}`))
//John: Cheeseburger
//Vijay: Chicken Tikka Masala
//Danielle: Vegan Lasagna
//function doNothing(){}: Did nothing
//NaN: yup NaN works too
```



## WeakMaps

- Regular Maps take a long time to search, especially if there several thousand keys to search through.
- WeakMaps are easier for the JavaScript engine to garbage collect on, making it easier for memory management.  
- Weakmaps do not have a list of the keys belonging to the object.
- WeakMaps are not enumerable (you can’t loop over them). 
- If you want to loop over them, you would need to manually keep track of the collection, however you’d also run into the issues of memory and garbage collection.  WeakMaps aren’t meant for that.  

**Let’s look an example using a `WeakMap`:**

```js
let myWeakMap = new WeakMap();

let obj1 = {}
let obj2 = {}
let obj3 = function() {}

myWeakMap.set(obj1, 'Chocolate Cake')
myWeakMap.set(obj2, 'Miso Soup')
myWeakMap.set(obj3, 'Salad')

console.log(myWeakMap.has(obj2)) //true
console.log(myWeakMap.get(obj3)) //Salad
```

## Sets

Sets are similar to Arrays in JavaScript, however the biggest distinction is that values in Sets must be **unique** (whereas Arrays can have repeating values).     

**Let’s look at an example below:**

```js
let mySet = new Set(); //use the add method to add new values to our set
mySet.add(1)
mySet.add(2)
mySet.add((24 / 12))
mySet.add("iPhone") //use the delete method to remove values from our set
mySet.delete(1)

console.log(mySet.has(2)) //true

mySet.forEach(val => console.log(val))
//2
//iPhone

console.log(mySet.size) //2
```

## WeakSets

WeakSets **do not** have a `size` or `forEach` method, thus they are extremely limited as far as use cases -- you can still `add`, `delete` and verify values with `has` in a `WeakSet`, however.

```js
let myWeakSet = new WeakSet()
//like WeakMaps, we can only add keys of type object to WeakSets

let w1 = {
  dayOfWeek: "Monday"
}

let w2 = {
  dayOfWeek: "Tuesday"
}

myWeakSet.add(w1)
myWeakSet.add(w2)
myWeakSet.delete(w2)

//no forEach method  
console.log(myWeakSet.has(w1)) //true
console.log(myWeakSet.has(w2)) //false
```

## Exercises

- What does a Proxy do in English?  How about in ES6?
- What is the difference between a `Map` and an `Array`?
- What is the difference between a `Set` and an `Array`?  A `Set` and a `Map`?
- What are three methods we can use to modify or verify values in a `Set`?
- What are some limitations of `WeakMaps`?  `WeakSets`?
