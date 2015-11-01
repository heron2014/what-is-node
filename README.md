# what-is-node
Quick introduction to Node.js

## What is Node.js

* It is a runtime environment that allows you to run Javascript code in the backend (eg on your computer), outside a browser
* The JS is executed by the V8 javascript engine (the thing that makes Google Chrome so fast)
* **Node.js uses an event-driven, non-blocking I/O model**
* Node ships with a lot of useful modules, so you don't have to write everything from scratch

## Who is using Node.js?

Netflix, New York Times, PayPal, LinkedIn, Uber and many more..

## Installation

If you don’t have the latest version of Node.js installed in your local machine you should do that
now: Head out to [Node.js](https://nodejs.org/en/) and click on the “Install” button.

## What are I/O operations?

I/O stands for input/output

![img](https://github.com/heron2014/what-is-node/blob/master/img/io.png)

Here are some common sources of I/O based programs:

* Databases (e.g. MySQL, PostgreSQL, MongoDB, Redis, CouchDB)
* APIs (e.g. Twitter, Facebook, Apple Push Notifications)
* HTTP/Web socket connections (from users of a web app)
* Files (e.g image resizer, video editor, internet radio)

## What is the event-driven, non-blocking I/O model?

Node is very good when you need to do several things at the same time.

Excellent explanation by [art-of-node](https://github.com/maxogden/art-of-node):

> For example, if you go down to a fast food joint and order a cheeseburger they will immediately take your order and then
 make you wait around until the cheeseburger is ready. In the meantime they can take other orders and start cooking cheeseburgers
 for other people. Imagine if you had to wait at the register for your cheeseburger, blocking all other people in line from
 ordering while they cooked your burger! This is called blocking I/O because all I/O (cooking cheeseburgers) happens one at a time.
 Node, on the other hand, is **non-blocking**, which means it can cook many cheeseburgers at once.

#### Basic example of the non-blocking, asynchronous model

```
setTimeout(function() {
console.log('World');
}, 2000);

console.log('2000');
```

Run this code in [repl.it](https://repl.it/) (or something similar).

You will see that first output is `2000`, and that the second is `World`.

`2000` is not 'blocked' by the previous operation, which needs 2 seconds to be completed.  Node just gets on executing the next function whilst the first one is still going on.  This is **asynchronous**.

If it *were* a blocking model, there would be a 2-second pause, followed by `World`, and *then* `2000`.  That would be *synchronous*.

#### Conclusion

When code runs **synchronously**, things happen strictly in order - you wait for it to finish before moving on to another task.
When you execute something **asynchronously**, you can move on to another task before it finishes.

## What is a callback - heart of Node.js

To fully understand how Node.js works you need to know what a callback is.

A '__callback__' is just a fancy term for a *function that is passed as __an argument to another function__*.

The point of callbacks is that you pass them into some function that runs asynchronously - it stores your callback away.  When that function is done with whatever it needs to do, it will call your callback with the necessary parameters.

**Example:**

Imagine we have some operation that runs for a long time, such as fetching some data from the database.  And let's say once we have that data, we want to run another function on it.

```
function getStuffFromDatabase() {
  // this takes a long time
}

function doSomethingWithData (dataFromDatabase) {
  // some data manipulation happens here
}

* other code *
...
...
```

We don't want our code to run synchronously and block other code from happening, which would waste precious time.  
But then our `doSomethingWithData` function mustn't run before the `dataFromDatabase` is ready.

So how do we make sure our `doSomethingWithData` function runs at the right time, whilst everything is running asynchronously?

We can re-write `dataFromDatabase` to take a function as an argument.  Then when we run `dataFromDatabase`, we can pass in `doSomethingWithData` as its argument.  

In other words, we'll use `doSomethingWithData` as a **callback function**.

```
function getStuffFromDatabase (callback) {
  // this takes a long time

  callback(dataFromDatabase);
}

function doSomethingWithData (dataFromDatabase) {
  // some data manipulation happens here
}

// Let's run the code now.
getStuffFromDatabase(doSomethingWithData);

* other code *
```
Note that the `doSomethingWithData` callback doesn't need invocation brackets - it will still work.


Let's test this out in real life.  
* We'll simulate taking a long time with a call to ```setTimeout```.
* We'll also pretend we got some data from the database, but we'll just hardcode a string value.
* Then we will console.log the data after the getting-from-database step is finished.

```
function getStuffFromDatabase (callback) {
  setTimeout(function() {
    var results = "database data";
  }, 5000);
};
```

Not quite finished: let's make `getStuffFromDatabase` call the callback function, passing to it our database data.

```
function getStuffFromDatabase (callback) {
  setTimeout(function() {
    var results = "database data";
    callback(results);
  }, 5000);
}
```

Now lets run the function with our callback function.

```
function displayData(data) {
  console.log(data);
}

getStuffFromDatabase(displayData);
```

You don't have to declare your callback function separately, by the way.  You can also pass it straight in as an anonymous function.  The following is *exactly* the same as above:

```
getStuffFromDatabase(function(data) {
  console.log("The database data is " + data);
});
```

So, as you can see ```data``` came from the function that you pass your callback into;
it gives that data to you when it knows what that data should be.




Look at this example.  This **won't** work how we want it to:

```
//  executed immediately      executed sometime in the future by getStuffFromDatabase
//      |                       |       
//      v                       v
getStuffFromDatabase(function(data) {
  var results = data; // <- this isn't available until sometime in the future!
});

console.log(results); // <- executed immediately
```

When the ```console.log``` runs we will get `undefined`.  Because of Node's non-blocking model, it will not wait for `getStuffFromDatabase` to complete - it will just move straight on to the next task.

When Node reaches the `console.log`, the assignment of ```var results``` hasn't happened yet!

So the reason you can't set a value in your callback and use it outside the callback is because the **callback itself doesn't happen
until later in time**.

### Conclusion

That's exactly how Node.js works, it requires you to **think non-linearly**. This means that you can fire off several async calls
to retrieve files, fetch data (or any event which takes time to complete) and **not lock the main thread** while it renders the content.

**The power of async** is being able to do **multiple things at the same time** without locking the main thread.

## How Node.js works under the hood

Node.js platform uses a **“Single Threaded Model with Event Loop”** architecture to handle multiple concurrent clients.


1. Client sends request to web server
2. Node.js web server receives those requests and places them into a **queue – known as a “event queue”**.
3. Node.js has a component internally, known as a the **“Event Loop”** - which picks up those requests one by one.
4. Event Loop uses a **single thread** only – which is a heart of Node.js model.
**Single Thread means that the program can run one piece of code at a time**.

    The event loop's job is:

    * to check whether a client request needs blocking I/O operations (like interacting with a database, file system or external API )
     or non-blocking I/O task
        * if a request is **non-blocking task**, it prepare response and send it back to the client
        * if a request is a **blocking I/O operation**, the event loop does not process this request.  Instead it picks up one thread
        from the internal **thread pool** and assigns this client request to it. In this way the event loop can serve other clients
        without locking the main thread.
        * This thread is responsible for taking that request, processing it,  preparing the response and sending it back to the event loop
        * The event loop, in turn, sends this response back to the client


![img](https://github.com/heron2014/what-is-node/blob/master/img/nodejs.png)


To understand more about the single-threaded versus multi-threaded model please refer [here](http://www.journaldev.com/7462/node-js-processing-model-single-threaded-model-with-event-loop-architecture)

## Why Javascript?

Ryan Dahl (creator of Node.js), wants a completely asynchronous net server

* Ruby VM was slow...
* C/C++ is too complicated
* Lua is perfect except its I/O libraries were only synchronous
* Enter JavaScript:
    * A naturally single-threaded/event-driven model, asynchronisation
    * almost blank area on the server side, so no issues of breaking old habits


## How do I start

Follow our next tutorial [here](https://github.com/node-girls/learn-node)

## Resources

* [NodeSchool.io interactive lessons](http://nodeschool.io/)
* [Node.js guide](http://nodeguide.com/)
* [The Art of Node (an introduction to Node)](https://github.com/maxogden/art-of-node/#the-art-of-node)
* [Absolute Beginners Guide To Node.js](http://blog.modulus.io/absolute-beginners-guide-to-nodejs)
* [Learn Node.js Completely and with Confidence](http://javascriptissexy.com/learn-node-js-completely-and-with-confidence/)
* [Excellent article by  Felix Geisendörfer](http://debuggable.com/posts/understanding-node-js:4bd98440-45e4-4a9a-8ef7-0f7ecbdd56cb)
* [Great explanation of the event loop](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
* [All other resources listed nicely here](http://stackoverflow.com/questions/2353818/how-do-i-get-started-with-node-js)
