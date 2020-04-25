# Chapter 4 - Callbacks, Promises, Iterators, Generator

Coming from ES5, you should be familiar with the idea of a “callback” function.  Even if you don’t know what it is, you’ve probably used one already.   

**For review, let’s look at the following example:**

```html
<div id="main"></div>
<style media="screen">
  #main {
    background: red;
    height: 70px;
    width: 70px;
  }
</style>

<script type="text/javascript">
var div = document.getElementById('main');
div.addEventListener('click', function(){
  alert("You clicked me!");
});
</script>
```

**Callback Function Example using `map`:**

```javascript
let map = (cb, ...list) => {
  let arr = [];
  for (let i of list) {
    arr.push(cb(i))
  }
  return arr;
}
let res = map(x => x * 2, 1, 2, 3)
console.log(res)
```

You can see now how JavaScript is quite powerful as a language, given we can define functions that callback other functions and allow us to create even more sophisticated functions like `map`.  These sophisticated functions are sometimes referred to as **higher level functions** by the JavaScript community.  

Instead of writing out a `for` loop to go through an array to multiply each item by 2, and then writing out another for loop to go through an array and do something else (e.g. division) when coding something -- we can use higher level functions with for loops built in, and apply callbacks with the custom functionality we want that should be applied to each item at runtime and save ourselves from writing repetitive code in our projects.  Use callback functions to your advantage!  

> This example was a little bit tricky, mainly because of the syntax (especially if you’ve never defined a callback function before -- but after writing your own callbacks for a while, you will get the hang of it).  I advise really studying this example in great detail if it seemed confusing to you.  Be sure to read slowly through every paragraph above as you examine the code.

> ProTip: Also remember, function definitions don't execute until we actually call them, so even if you see function calls in function definitions, nothing actually happens until the main function gets called.  Always look at the definition of the function after you see a function call somewhere else in the program to preserve your sanity!

### Callback Hell

Callbacks are definitely useful, but what if you wanted to do *a series of operations, one callback after another*?  Up until now, we've just discussed using a single callback within a function.

**Here are some common tasks for web developers coding an application:**
- Get weather from a weather API
- Allow login with Facebook to see who’s accessed the site (Facebook's Open Graph API is notorious for callback hell)
- Install Google Maps  

You can imagine each of these tasks as being code blocks that have to occur after our web app starts, (they can't all start at the same time), and some items may be dependent on other code blocks finishing first successfully.  If something breaks, the whole app or website can break too!  How do we manage such programs?  Let's look at some examples below using what we know about callbacks.

**Callback Functions with AJAX**

```javascript
let ajax = (http) => {
  if (!http) {
    throw new Error("Must define an http object")
  }
  let xhr = new XMLHttpRequest();
  xhr.open(http.type, http.url);
  xhr.onload = () => {
    http.success(null, xhr.response)
  };
  xhr.onerror = () => {
    http.error(xhr.response)
  };
  xhr.send();
}
```

**Calling the AJAX function**

```javascript
ajax({
  //Get data in the form of a list of things todo from this url
  url: "https://jsonplaceholder.typicode.com/todos/1",
  type: "GET",
  success: function(err, data) {
    if (err) {
      throw err;
    }
    let res = JSON.parse(data);
    //Now that we have the things to do from this user, let's see his posts to
    //using this new url and call it with another ajax function     
    let url = "https://jsonplaceholder.typicode.com/posts/" + res.userId;
    ajax({
      url: url,
      type: "GET",
      success: function(err, data) {
        console.log(data);
        ajax({
          /* Great we executed two ajax calls successfully, let's go on
          to do a few more steps like connect to Facebook, Google Maps, etc. Aren't callbacks fun?? */
        })
      },
      error: function(res) {
        //Oh no, what if we get an error? Should we do another ajax call here?
        console.log(err);
      }
    })
  },
  error: function(res) {
    //Oh no, the main url didn't work! What do we do now? Another ajax call?
    console.log(err);
  }
});
```

Is there a better way to handle multiple steps making `ajax` calls to various websites that doesn't ensure in callback hell?  Yes-- you’re in luck!

## Promises

Promises sound scary, but they are actually quite straightforward.  There are a few things to keep in mind when working with promises.  

**Basic Promise Syntax**

```javascript
Promise((resolve, reject) => {
  if (/* some condition met */) {
    resolve()
  } else {
    reject()
  }
})
```


```javascript
//We are now saving the result of a Promise in a variable p1
//
let p1 = new Promise((resolve, reject) => {
  if ( /*some condition is true*/ ) {
    resolve()
  } else {
    reject()
  }
})
console.log(p1)
p1.then(result => { console.log(result) })
```

By saving `p1` as a `new Promise` object, `p1` gets the special `then` method, which allows us to see the result of the `Promise` and possibly do something with it later (for asynchronous programming).   

So far so good? Great. Here’s where Promises really shine.  Using the `then` method, we can **return another `Promise` object from `p1`**.  

```javascript
//We can also return another Promise inside our then method
p1.then(result => {
  let data = result;
  console.log(result)
  return new Promise((resolve, reject) => {
    if ( /*some other condition is true*/ ) {
      resolve()
    } else {
      reject()
    }
  })
}).then(result => {
  console.log(result)
})
```

So why Promises you ask?  The answer is **managed asynchronous programming**.

By using the `then` method, we can check the result of a `Promise`, run some logic, and if we wanted to, start a new `Promise` chain all over again without things getting difficult to read or code moving slowly over to the right.  

We essentially avoid callback hell here; now our function callbacks aren’t nested one after another, instead Promises limit our asynchronous work to one block at a time.

If a block completes, we can either stop or start a new Promise. Feeling better already right?

**Managed Callbacks with Promises**

```javascript
//Managed callback hell
p1.then(result => {
  let d1 = result;
  return new Promise((resolve, reject) => {
    ...
  })
}).then(result => {
  let d2 = result;
  return new Promise((resolve, reject) => {
    ...
  })
}).then(result => {
  let d3 = result;
  return new Promise((resolve, reject) => {
    ...
  })
}).then(result => {
  console.log(result)
})
```

As you can see above, there’s a lot of Promises here, but the good news is it’s all readable, and easy to debug at any step of the way (if something goes wrong, we’ll see a `reject` message and be able to address it appropriately).  This wasn’t possible with what we were doing in the previous section with `ajax` and callback functions like `http.success` and `http.error`. 

**Revisiting Our AJAX example:**

Let’s look at our previous AJAX example now modified for Promises.  We’ve renamed our `ajax` function `ajaxP` (`P` for `Promise`):

```javascript
let ajaxP = (http) => {
  return new Promise(function(resolve, reject) {
    if (!http) {
      throw new Error("Must define an http object")
    }
    let xhr = new XMLHttpRequest();
    xhr.open(http.type, http.url);
    xhr.onload = function() {
      if (this.status >= 200 && this.status < 300) {
        resolve(xhr.response);
      } else {
        reject({
          status: this.status,
          statusText: xhr.statusText
        });
      }
    };
    xhr.onerror = function() {
      reject({
        status: this.status,
        statusText: xhr.statusText
      })
    }
    xhr.send();
  });
}
```

The `ajaxP` function still takes in an `http` object but now returns a `new Promise` object we can use later to see our result with the `then` method, `ajaxP.then(/*callback function*/)`.

Inside the `Promise`, we swapped `http.success` and `http.error` functions with `resolve` and `reject` functions.   This `ajaxP` function is also a little more specific; if the status code that comes back from `xhr` is from `200` to `300`, we can `resolve` the url.  If not, then we’ll send back an error -- maybe the status was 404 (code for webpage not found).  

> A status code that comes back from an `xhr` request is much like a status code you might see when you visit a website -- e.g. the most famous status code is `404`, Page Not Found.  If we see a `404` in our `xhr` object, our code executes the `reject` function we defined. `404` is not considered an error with our code, because our `xhr.onload` event was actually successful in visiting the url provided (this was a server side error).  

> If there was something really wrong with our request, our code would jump down to `xhr.onerror`, where we could see what went wrong with our own code -- basically a client side error (or our error) e.g. our internet connection is not good. If we get any status between `200` to `300` with `xhr` requests, that usually means the data can be found and we can do something with it using the `resolve` function we defined.


**Next, let’s look at what happens when we call the ajaxP function:**

```javascript
//Promisified version of our http request
ajaxP({
  type: "GET",
  url: "https://jsonplaceholder.typicode.com/todos/1"
}).then(response => {
  return JSON.parse(response);
}).then(response => {
  console.log(response)
  let user = response.userId
  let url = `https://jsonplaceholder.typicode.com/users/${user}`
  return ajaxP({
    type: "GET",
    url: url
  })
}).then(res => {
  console.log(JSON.parse(res))
})
```


**`catch` method on Promises**

The `catch` method on Promises helps us debug scenarios where our Promise fails; basically when the `reject` method is called on a `Promise` (instead of `resolve`).  Here’s the basic syntax for using it:

```javascript
p1.then(result => { /* some code */ }).catch(error => { console.log(error) })
```
Going back to our `ajaxP` example, if we add the `catch` method to the end of our `ajaxP` function call, we can also account for any errors that might arise if something goes wrong with our `ajaxP Promise` now.  Handy!  

```javascript
//Promisified version of our http request
ajaxP({
  type: "GET",
  url: "https://jsonplaceholder.typicode.com/todos/1"
}).then(response => {
  return JSON.parse(response);
}).then(response => {
  console.log(response) let user = response.userId
  let url = `https://jsonplaceholder.typicode.com/users/${user}`
  return ajaxP({
    type: "GET",
    url: url
  })
}).then(res => {
  console.log(JSON.parse(res))
}).catch(error => { //oops our Promise was rejected, why?   
  console.log(error)
})
```

As you can see managing callbacks with Promises are so much easier than trying to do callback hell.  Everything is accounted for now including success and error callbacks.  One last thing to review before we go on to the next section are Promise states:

**Promise States**

Often, while using `console.log` to look at your code, you’ll see your `Promise` shows the *state* it’s currently in.  There are three main states for Promises:

```text
- Pending
- Rejected
- Fulfilled
```


## Iterators:

At the very base level, **iterators** are objects that can be used to keep track of a collection such as an array or an object.   Iterators have a method called `next`, which returns an object with two properties, `value` and `done`.  

```javascript
let myArr = [1,2,3,4]  //a standard array   
let itr = myArr.values() //returns an iterator
console.log(itr.next()) //we call the iterator's next method  
// {value: 1, done: false}
```


>Note that in this example, `myArr.values()` returns an iterator. The `values` method comes from `Array.prototype.values` which is a built-in JavaScript function available on all arrays.

**Rewriting this example:**

```javascript
let myArr = [1, 2, 3, 4]
let itr = myArr.values()
let i = itr.next();
//{value: 1, done: false}  

/* If we wanted to keep getting all the values, we can keep  
calling the iterator's next method, like so with a while loop   
*/

while (!i.done) {
  console.log(i);
  //show the current object with value and done properties
  i = itr.next();
  //call itr's next method and save the resulted object in i.
}
```

You might look at iterators and think they are a complicated way of going about getting a next value from an object.  Why not just use a `for` loop, or better yet the new `for..of` loop?  Well actually, the `for..of` loop is *syntactic sugar* for writing an iterator.  

The `for..of` loop saves us the step of having to call the `next` method, parse the object returned from an iterator, look at whether its `done` property is `true`, and get the next value from the object being iterated over.  We'll see how we can use the `for..of` loop later when it comes to saving time with iterators.

```javascript
let myArr = [1,2,3,4];

for(let i of myArr){   
  console.log(i);
}
```
With iterators, we can control the flow of a loop.  If this sounds like we are using an iterator to **make our programming more asynchronous, you’re absolutely correct!**

## Generators:

Also new to ES6 are **generator** functions.  Generators are unique functions in that they can be made to `yield` multiple `return` values, as opposed to regular functions which only `return` one value.

Generators **pause and resume** execution, so that after each `yield` statement, we pass back control to its function caller elsewhere in our program, run some other code, then come back to our function caller, call the generator again and `yield` another result.  This is because behind the scenes, generators actually **`return`** iterators, which we use like we discussed in the previous section to asynchronously loop through the `yield` statements defined in the generator function.  

Let’s look at some examples to see how generators work and help clear up any confusion:

**The basics:**

```javascript
function* myGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

//generators return iterators
let itr = myGenerator();

//use the next method to get the next yielded value of myGenerator
let res = itr.next();
console.log(res.value); //1  

/* do something else in our code ... */

//time to use our generator again, use next to get the next yield
res = itr.next();
console.log(res.value);
//2

/* keep doing other stuff in our code.. maybe that Facebook API ajaxP request */
```

Generator functions are defined by the `*` (asterisks mark) after the `function` keyword.  If you don’t use the asterisks, your function will only be a regular JavaScript function.

> Note you can't use arrow function syntax or function expression syntax with generators.  You have to define a generator using function literal syntax, e.g. `function* name() { /* body of generator function */ }`.


```javascript
function* myGenerator() {
  yield 1;
  yield 2;
  yield 3;
}
for (let i of myGenerator()) {
  console.log(i)
}
//1
//2
//3
```
Easy, peasy.  I suppose you now want a real world example.  Well, you're in luck!

**Here’s a real world example with Generators**

Imagine we have a website that needs to create a new `id` in sequence (starting from `1` to `Infinity`) each time we have a new account created.  If you think back to your ES5 days, how would you accomplish this?  

**Let’s use a generator instead to create the `id`:**

```javascript
//generator function
function* createNewId() {
  let i = 0;
  //an infinite loop! but should we be worried?
  while (true) {
    yield i; //yield us infinite ids
    i++;
  }
}

//get an iterator from createNewId() generator
let itr = createNewId()

function createNewAccount(email) {
  //allows us to step through our generator, one step at a time
  //we're controlling the loop but getting a new id each time!
  let res = itr.next();
  let id = res.value;

  //note the use of single notation here
  let user = {
    email,
    id
  }
  console.log("new user created", user)
  //save to db if desired
}

createNewAccount("aj@iw.org")
//new user created Object { email: "aj@iw.org", id: 0 }
createNewAccount("dt@rp.org")  
//new user created Object { email: "dt@rp.org", id: 1 }
createNewAccount("md@md.com")  
//new user created Object { email: "md@md.com", id: 2 }
```

In summary: This `id` generation continues to occur, basically forever, so long as we keep needing new accounts.  The loop in the generator is an infinite loop, but our code executes whenever we want, so long as we want to call `createNewAccount` with a new `email`. Pretty awesome, right?

If you’re with me so far, good job.  If you need to re-read thru all the material we covered in this chapter, go ahead, as the next chapter will build on all the examples we’ve covered so far.    

In the next chapter, we’ll cover more complicated uses of iterators, generators, promises and even look at the new `async`/`await` functions as well as the `fetch` library.   You’re in for a real treat!

## Exercises

- What does it mean when someone says a program is running synchronously?
- What are some ways asynchronous programming is better than traditional synchronous programming?
- What is a callback function?  What are some examples of callback functions?
- Why is callback hell a problem for developers?
- How do Promises solve the problem with callback hell?
- What are the different states of a Promise?
- What is an iterator and how is it different from a traditional `for` loop?
- How is an iterator similar to a `for..of` loop?
- What does `yield` mean in a generator?
- How can generators help us manage our asynchronous code?  Can you write out an example?
