# what-is-node
Quick introduction to Node.js

## What is Node.js

* It is a runtime environment that allows you to run Javascript code in the backend (eg on your computer), outside a browser
* The JS is executed by the V8 javascript engine (the thing that makes Google Chrome so fast)
* **Nodejs uses event-driven, non-blocking I/O model**
* Node ships with a lot of useful modules, so you don't have to write everything from scratch

## Who is using Node.js

Netflix, New York Times, PayPal, LinkedIn, Uber and many more.. 

## Installation

If you don’t have the latest version of Node.js installed in your local machine you should do that
now: Head out to [node.js](https://nodejs.org/en/) and click on the “Install” button.

## What are I/O operations

I/O stands for input/output

![img](https://github.com/heron2014/what-is-node/blob/master/img/io.png)

Here are some common sources of I/O based programs:

* Databases (e.g. MySQL, PostgreSQL, MongoDB, Redis, CouchDB) 
* APIs (e.g. Twitter, Facebook, Apple Push Notifications)
* HTTP/WebSocket connections (from users of a web app)
* Files (e.g image resizer, video editor, internet radio)

## What is event-driven, non-blocking I/O model

Node is very good when you need to do several things at the same time.

Excellent explanation by [art-of-node](https://github.com/maxogden/art-of-node):

> For example, if you go down to a fast food joint and order a cheeseburger they will immediately take your order and then
 make you wait around until the cheeseburger is ready. In the meantime they can take other orders and start cooking cheeseburgers
 for other people. Imagine if you had to wait at the register for your cheeseburger, blocking all other people in line from 
 ordering while they cooked your burger! This is called blocking I/O because all I/O (cooking cheeseburgers) happens one at a time.
 Node, on the other hand, is **non-blocking**, which means it can cook many cheeseburgers at once.

#### Basic example of non blocking asynchronous model

```
setTimeout(function() {
console.log('World');
}, 2000);

console.log('2000');
```

Run this code for example in [repl.it](https://repl.it/) 
and you will see that first output is 2000 which is not blocked by previous operations which needs 2 seconds to be completed. 

## What is callback - heart of Node.js

To fully understand how Node.js works you need to know what is callback

The point of callbacks is that you pass them into some function that runs asynchronously;it stores your callback away,
and when that function is done with whatever it needs to do, it will call your callback with the necessary parameters.

Note: 

When you execute something **synchronously**, you wait for it to finish before moving on to another task.
When you execute something **asynchronously**, you can move on to another task before it finishes.

**Example:**

Imagine we have some operation that runs for a long time, fetching some data from the database

```
function getStuffFromDatabase() {
  // this takes a long time
};
```

Since we don't want it to run synchronously, we'll pass in a callback

```
function getStuffFromDatabase(callback) {
  // this takes a long time
}
```

We'll simulate taking a long time with a call to ```setTimeout;```
we'll also pretend we got some data from the database, but we'll just hardcode a string value.

```
function getStuffFromDatabase(callback) {
  setTimeout(function() {
    var results = "database data";
  }, 5000);
};
```

Finally, once we have the data, we'll call the callback function.

```
function getStuffFromDatabase(callback) {
  setTimeout(function() {
    var results = "database data";
    callback(results);
  }, 5000);
};
```

Call the function with callback that do something with your data

```
getStuffFromDatabase(function(data) {
  console.log("The database data is " + data);
});
```

So, as you can see ```data``` came from the function that you pass your callback into; 
it gives that data to you when it knows what that data should be.

The reason you can't set a value in your callback and use it outside the callback is because the **callback itself doesn't happen
until later in time**.

```
//  executed immediately      executed sometime in the future by getStuffFromDatabase
//      |                       |       
//      v                       v
getStuffFromDatabase(function(data) {
  var results = data; // <- this isn't available until sometime in the future!
});

console.log(results); // <- executed immediately
```

When the ```console.log``` runs, the assignment of ```var results``` hasn't happened yet!

### Conclusion

That's exactly how Node.js works, it requires you to **think non-linearly**. This means that you can fire off several async calls 
to retrieve files, fetch data (any event which take time to complete) and **not lock the main thread** while it renders the content.

**The power of async** is being able to do **multiple things at the same time** without locking the main thread.

## How Node.js works under the hood

Node.js platform uses **“Single Threaded Model with Event Loop”** architecture to handle multiple concurrent clients.


1. Client send request to Web Server
2. Node.js Web Server receives those requests and place them into a **Queue – known as “Event Queue”**.
3. Node.js has a component internally, known as a **“Event Loop”** - which picks up those requests one by one. 
4. Event Loop uses **Single Thread** only – which is a heart of Node.js model.
**Single Thread means that the program can run one piece of code at a time**

    Event Loop job is:
    
    * to check whether client request require blocking I/O operations (like interacting with DB, File System or External API )
     or non blocking I/O task
        * if request is **non blocking task**, it prepare response and send it back to the client
        * if request is **blocking I/O operation**, Event Loop does not process this request,instead it picks up one thread 
        from internal **Thread Pool** and assign this client request to it. In this way Event loop can serve other clients 
        without locking the main thread.
        * this thread is responsible for taking that request, process it,  prepare response and send it back to the Event Loop
        * Event Loop in turn, sends this response to respective client
        
        
![img](https://github.com/heron2014/what-is-node/blob/master/img/nodejs.png)
 
 
To understand more about single threaded versus multi-threaded model please refer [here](http://www.journaldev.com/7462/node-js-processing-model-single-threaded-model-with-event-loop-architecture)

## Why Javascript? 

Ryan Dahl (creator of Node.js), wants a completely asynchronous net server 

* Ruby VM was slow... 
* C/C++ is too complicated 
* Lua is perfect except it has existed synchronous I/O libraries 
* Here JavaScript comes: 
    * natural single-threaded/event-driven model, asynchronization 
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
* [Great explanation what is event-loop](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
* [All other resources listed nicely here](http://stackoverflow.com/questions/2353818/how-do-i-get-started-with-node-js)