# How JavaScript Code Gets Executed: Synchronous vs. Asynchronous


## In this post, we will go through a high-level overview of how synchronous and asynchronous JavaScript code gets executed by the JavaScript engine.

A JavaScript engine is a program that executes JavaScript code and converts it to a language the computer understands. Every web browser contains a JavaScript engine. For example, V8 is the JavaScript engine in Google Chrome and Node.js. Different browsers have different JavaScript engines, but they implement the same basic concept under the hood.
Before we go into more details, let’s go through some of the terms used in this post:

Execution context: An execution context is an environment where JavaScript code runs and executes. A new execution context is created whenever a function is called or invoked. We have two types of execution contexts: Global execution context and function execution context. And an execution context has two phases: memory creation and code execution.

Variable environment: A variable environment is where the JavaScript engine allocates memory in key-value pairs to the variables and functions within an execution context.
Call stack: The call stack is a part of the JavaScript engine that helps keep track of function calls. When a function gets invoked, it is pushed to the call stack where its execution begins, and when the execution is complete, the function gets popped off the call stack. It uses the concept of stacks in data structures that follows the Last-In-First-Out (LIFO) principle.

# Synchronous JavaScript
JavaScript is synchronous, blocking and single-threaded. This means that the JavaScript engine executes our program sequentially, one line at a time from top to bottom in the exact order of the statements.

<!-- console.log("One")
console.log("Two")
console.log("Three") -->

The JavaScript engine cannot execute the second console.log statement before the first one, and the third one can’t be executed before the second one. This is what I mean when I say JavaScript is synchronous, and it processes our script line by line. Until a current task is completed, the next task cannot begin.


# Asynchronous JavaScript
Unlike synchronous operations, an asynchronous operation does not block the next task from commencing even if the current task isn’t complete yet. The JavaScript engine works with additional features called Web APIs (setTimeout, setInterval, etc.) in the web browser, which allows JavaScript to behave asynchronously.

With the help of these Web APIs, JavaScript can move certain tasks to the browser while JavaScript continues executing the synchronous operations. As a result of this asynchronous behavior, if we have a task that may take some time (accessing a database, file system operations, etc.), the asynchronous task can be handed off to the browser to happen in the background without blocking the next task.

In the example below, I’ll use a setTimeout() function to demonstrate an asynchronous operation. I won’t include details on how memory gets allocated because I already explained that above.

console.log("first")

setTimeout(() => {
  console.log("second");
}, 3000)

console.log("third")
JavaScript
When this code runs, the following will happen:

A global execution context will be created and added to the call stack.

On the first line, we have console.log("first"). An execution context will be created for it and pushed to the call stack, first will be printed to the console, then popped off the call stack.

On the next line, we have a setTimeout() function, which is one of the browser’s Web APIs. It takes two parameters: a callback function as the first parameter and the time (specified in ms) you want to wait before executing the callback function as the second parameter. A new execution context is created and pushed to the stack. Because setTimeout is a Web API, the Web API will register the callback function passed to setTimeout in the API environment and trigger the timer in the browser for 3000ms, Then setTimeout is popped off the call stack.

On the next line, we have console.log("third"). An execution context is created and added to the call stack, third is printed in the console, and then the function is popped off the call stack.

In the Web API environment, we still have the callback function passed to setTimeout, waiting for the timer to expire after 3000 milliseconds.

Let’s say the timer is up. The callback function can’t be moved directly from the Web API environment to the call stack for its execution. It has to wait its turn, so it is first moved to the callback queue to wait until all the synchronous operations have been executed and the call stack is empty. If we had a thousand operations after the setTimeout function, they would all be executed before the callback function for setTimeout is moved to the call stack.

The event loop is responsible for moving asynchronous tasks from the callback queue to the call stack whenever the call stack is empty. The call stack is empty now, so the event loop moves the callback function to the call stack for its execution, and a new execution context is created for it.

We have console.log("second") inside the callback function. The statement is added to the call stack, and second is printed to the console, then it is popped off the call stack. Now at the top of the call stack, we have the callback function, and its execution is complete, so it gets popped off the call stack. We’re back to the global execution context, and since there’s nothing left to be executed, it also gets popped off the call stack.

Apart from the callback queue, we also have the microtask queue, which has a higher priority. Callback functions from promises and mutation observers are added to the microtask queue. When a promise is ready, the promise callback is added to the microtask queue, where it has to wait for its execution.

The event loop repeatedly moves callback functions from the microtask queue and the callback queue to the call stack, but the microtask queue has a higher priority than the callback queue. Callback functions queued in the microtask queue will all be moved to the call stack, one at a time before any callback from the callback queue moves. When the microtask queue is empty, the event loop can start moving callbacks in the callback queue to the call stack.

Conclusion# lecture_async
