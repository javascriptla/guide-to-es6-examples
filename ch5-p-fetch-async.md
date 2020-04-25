# Chapter 5 - More on Promises, Fetch, Async & Await

## Promise.resolve

The `resolve` method allows you to use a `Promise` quickly, instead of typing out all the overhead of writing out a new `Promise` with `resolve`/`reject` syntax.  Sometimes having a method like this is handy, especially if you just need to do some fast testing.

```js
Promise.resolve(1); //resolves the number 1.  

Promise.resolve(setTimeout(function() {
  console.log('finished your tea') //finished your tea
}, 2000));
```

## Promises and Order

We already know that promises take time to complete, but they also don’t finish in the order you might expect.   You aren’t guaranteed they will finish in the order you write them out line by line.  Let’s look at an example for a little more clarity:

```js
let get = (url) => {
  return new Promise(function(resolve, reject) {
    let xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = function() {
      if (this.status >= 200 && this.status < 300) {
        resolve(xhr.response);
      } else {
        reject({
          status: this.status,
          statusText: xhr.statusText
        });
      }
    }
    xhr.onerror = function() {
      reject({
        status: this.status,
        statusText: xhr.statusText
      })
    }
    xhr.send();
  });
}

let p1 = get('https://jsonplaceholder.typicode.com/todos/1');
let p2 = get('https://jsonplaceholder.typicode.com/todos/2');
let p3 = get('https://jsonplaceholder.typicode.com/todos/3');

p1.then(data => console.log(JSON.parse(data)));
p2.then(data => console.log(JSON.parse(data)));
p3.then(data => console.log(JSON.parse(data)));
```


## Promise.all

With `Promise.all`, you can control the order that promises finish in.  Each promise is loaded into an array and executed in order received.   Once all Promises are fulfilled, we can display the results using the `then` method.  Let’s look at a revised example using the code from before:

```js
let p1 = get('https://jsonplaceholder.typicode.com/todos/1')
let p2 = get('https://jsonplaceholder.typicode.com/todos/2')
let p3 = get('<https://jsonplaceholder.typicode.com/todos/3>')

/*
Promise.all replaces our separate p1, p2, p3 .then calls.
We now load up p1, p2, p3 into an array then get the results
back in an array with results back in the same order they
were loaded in
*/

Promise.all([p1, p2, p3])
.then((data)=>console.log(data))
```

Now our requests are in order!

`Promise.all` runs all three promises until all the promises are completed, and then we log out our final value as an array of all three `Promise` values.  

**Let’s look at another example:**

```js
let p1 = new Promise((res, rej) => {
  setTimeout(() => {
    res(12)
  }, 3000)
}) let p2 = new Promise((res, rej) => {
  setTimeout(() => {
    res(23)
  }, 1000)
}) let p3 = new Promise((res, rej) => {
  setTimeout(() => {
    res(43)
  }, 5000)
})

Promise.all([p1, p2, p3])
.then((values) => console.log(values))
```


## Promise.race

`Promise.race` is similar to `Promise.all` in that it also takes an array/iterable of values and executes each `Promise` in sequence.   However, the *key difference* is that `Promise.race` returns the first `Promise` in the array that finishes first.   *It’s a race to the finish!*  

**Using our example from before:**

```js
let p1 = new Promise((res, rej) => {
  setTimeout(() => {
    res(12)
  }, 3000)
}) let p2 = new Promise((res, rej) => {
  setTimeout(() => {
    res(23)
  }, 1000)
}) let p3 = new Promise((res, rej) => {
  setTimeout(() => {
    res(43)
  }, 5000)
})

//Which Promise will come back first?
Promise.race([p1, p2, p3]).then((values) => {
  console.log(values)
})
//23
```
`23` comes back because it was the fastest to complete

Sometimes you might not care about all the promises you might have in execution, you only care for the one that comes back fastest (perhaps your application is connecting to multiple databases and just wants the fastest one).  It’s nice to have a method like `Promise.race`.

## Promises with Generators


```js
let itr = myGeneratorFunc(); //returns an iterator
let res = itr.next(); //get {value:someValue, done:false }

let p1 = res.value //yield first Promise

//execute Promise
p1.then((val) => {
  console.log(val) //output JSON
})

/* -- lines of code later -- */

res = itr.next(); //get next iteration {value:someValue, done:false}
let p2 = res.value; //yield second Promise

//execute Promise
p2.then((val) => {
  console.log(val) //output JSON
})

/* -- still more lines of code later -- */

//Rinse and repeat as necessary
//Our generator holds all GET requests  
function* myGeneratorFunc() {
  yield get('https://jsonplaceholder.typicode.com/todos/1')
  yield get('https://jsonplaceholder.typicode.com/todos/2')
  yield get('https://jsonplaceholder.typicode.com/todos/3')
}
```

Now, using generators, we can run a couple promises, expect their order to be maintained, then go on to some other work, and come back later again to run more promises if we needed to.   Pretty awesome right?

## fetch API

**Here’s an example using the GET method (from JSON placeholder’s website):**

```js
fetch('https://jsonplaceholder.typicode.com/todos/1')
.then(response => response.json())
.then(json => console.log(json))
```
  

**Here’s an example using the `POST` method**

The `POST` method is used to send data to a server.  Let’s look at an example with `fetch`:

```js
fetch('https://jsonplaceholder.typicode.com/posts', {
  method: 'POST',
  body: JSON.stringify({
    player: 'Player 1',
    userId: 1
  }),
  headers: {
    "Content-type": "application/json; charset=UTF-8"
  }
})
.then(response => response.json())
.then(json => console.log(json))
```

This method looks similar to the `GET` request, in that we still pass in a `URL`, however we also supply an object (makes sense-- we are sending data in to the server).   

The object needs to specify the method, `POST`, along with the body of information (in `JSON` format) that needs to be added to the database -- in this case it’s a new `player`: `Player 1`, with an `userId` of `1`.   We also send in our headers information so that the receiving database knows how to process the data once received (`'Content-type': 'application/json'`).

## async / await

`async` and `await` are newer ES6 features that make `Promise` based programming feel more like synchronous programming; and that give us control over exactly when a `Promise` should execute -- without the need for generators or iterators or complex syntax.

**Let’s look at an example:**

```js
//Basic function that returns a new Promise object  
function promiseMeYoullWait() {
  return new Promise((resolve, reject) => {
    try {
      setTimeout(() => {
        resolve('You waited!')
      }, 4000)
    } catch (e) {
      reject(`Sorry the program couldn't wait, Error: ${e}`)
    }
  })
}

//Note the use of the async keyword before function keyword
async function myAsync(){
  let res = await promiseMeYoullWait() //await the Promise result
  console.log(res)
}
myAsync();
```


## Exercises

- Why do Promises not return in the order we define them?
- What does `Promise.resolve` do?  
- What's the difference between `Promise.race` and `Promise.all`?
- What's better about `fetch` than something like using an `ajax` function?
- What problem does `async` / `await` solve?  Can you write out an example?
