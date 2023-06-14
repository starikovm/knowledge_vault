What the heck event loop anyway? - https://www.youtube.com/watch?v=8aGhZQkoFbQ

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop


## [Event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop#event_loop)

The **event loop** got its name because of how it's usually implemented, which usually resembles:

```
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

`queue.waitForMessage()` waits synchronously for a message to arrive (if one is not already available and waiting to be handled).

### ["Run-to-completion"](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop#run-to-completion)

Each message is processed completely before any other message is processed.

This offers some nice properties when reasoning about your program, including the fact that whenever a function runs, it cannot be preempted and will run entirely before any other code runs (and can modify data the function manipulates). This differs from C, for instance, where if a function runs in a thread, it may be stopped at any point by the runtime system to run some other code in another thread.

A downside of this model is that if a message takes too long to complete, the web application is unable to process user interactions like click or scroll. The browser mitigates this with the "a script is taking too long to run" dialog. A good practice to follow is to make message processing short and if possible cut down one message into several messages.

### [Adding messages](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop#adding_messages)

In web browsers, messages are added anytime an event occurs and there is an event listener attached to it. If there is no listener, the event is lost. So a click on an element with a click event handler will add a message — likewise with any other event.

The first two arguments to the function [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) are a message to add to the queue and a time value (optional; defaults to `0`). The _time value_ represents the (minimum) delay after which the message will be pushed into the queue. If there is no other message in the queue, and the stack is empty, the message is processed right after the delay. However, if there are messages, the `setTimeout` message will have to wait for other messages to be processed. For this reason, the second argument indicates a _minimum_ time — not a _guaranteed_ time.

Here is an example that demonstrates this concept (`setTimeout` does not run immediately after its timer expires):

```
const seconds = new Date().getTime() / 1000;

setTimeout(() => {
  // prints out "2", meaning that the callback is not called immediately after 500 milliseconds.
  console.log(`Ran after ${new Date().getTime() / 1000 - seconds} seconds`);
}, 500);

while (true) {
  if (new Date().getTime() / 1000 - seconds >= 2) {
    console.log("Good, looped for 2 seconds");
    break;
  }
}
```

Copy to Clipboard

### [Zero delays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop#zero_delays)

Zero delay doesn't mean the call back will fire-off after zero milliseconds. Calling [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) with a delay of `0` (zero) milliseconds doesn't execute the callback function after the given interval.

The execution depends on the number of waiting tasks in the queue. In the example below, the message `"this is just a message"` will be written to the console before the message in the callback gets processed, because the delay is the _minimum_ time required for the runtime to process the request (not a _guaranteed_ time).

The `setTimeout` needs to wait for all the code for queued messages to complete even though you specified a particular time limit for your `setTimeout`.

```
(() => {
  console.log("this is the start");

  setTimeout(() => {
    console.log("Callback 1: this is a msg from call back");
  }); // has a default time value of 0

  console.log("this is just a message");

  setTimeout(() => {
    console.log("Callback 2: this is a msg from call back");
  }, 0);

  console.log("this is the end");
})();

// "this is the start"
// "this is just a message"
// "this is the end"
// "Callback 1: this is a msg from call back"
// "Callback 2: this is a msg from call back"
```

Copy to Clipboard

### [Several runtimes communicating together](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop#several_runtimes_communicating_together)

A web worker or a cross-origin `iframe` has its own stack, heap, and message queue. Two distinct runtimes can only communicate through sending messages via the [`postMessage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) method. This method adds a message to the other runtime if the latter listens to `message` events.