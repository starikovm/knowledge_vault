
https://medium.com/@_lrlna/garbage-collection-in-v8-an-illustrated-guide-d24a952ee3b8


# Memory management
 - JavaScript is one of the so called _garbage collected_ languages
 - The main cause for leaks in garbage collected languages are _unwanted references_.

### Mark-and-sweep

Most garbage collectors use an algorithm known as _mark-and-sweep_. The algorithm consists of the following steps:

1.  The garbage collector builds a list of "roots". Roots usually are global variables to which a reference is kept in code. In JavaScript, the "window" object is an example of a global variable that can act as a root. The window object is always present, so the garbage collector can consider it and all of its children to be always present (i.e. not garbage).
2.  All roots are inspected and marked as active (i.e. not garbage). All children are inspected recursively as well. Everything that can be reached from a root is not considered garbage.
3.  All pieces of memory not marked as active can now be considered garbage. The collector can now free that memory and return it to the OS.

## The Three Types of Common JavaScript Leaks

### 1: Accidental global variables

```javascript
function foo(arg) {
    bar = "this is a hidden global variable";
}
```

Is in fact:

```javascript
function foo(arg) {
    window.bar = "this is an explicit global variable";
}
```

Another way in which an accidental global variable can be created is through `this`:

```javascript
function foo() {
    this.variable = "potential accidental global";
}

// Foo called on its own, this points to the global object (window)
// rather than being undefined.
foo();
```

To prevent these mistakes from happening, add `'use strict';` at the beginning of your JavaScript files. This enables a stricter mode of parsing JavaScript that prevents accidental globals.

### 2: Forgotten timers or callbacks

```javascript
var someResource = getData();
setInterval(function() {
    var node = document.getElementById('Node');
    if(node) {
        // Do stuff with node and someResource.
        node.innerHTML = JSON.stringify(someResource));
    }
}, 1000);
```

This example illustrates what can happen with dangling timers: timers that make reference to nodes or data that is no longer required. The object represented by `node` may be removed in the future, making the whole block inside the interval handler unnecessary. However, the handler, as the interval is still active, cannot be collected (the interval needs to be stopped for that to happen). If the interval handler cannot be collected, its dependencies cannot be collected either. That means that `someResource`, which presumably stores sizable data, cannot be collected either.

```javascript
var element = document.getElementById('button');

function onClick(event) {
    element.innerHtml = 'text';
}

element.addEventListener('click', onClick);
// Do stuff
element.removeEventListener('click', onClick);
element.parentNode.removeChild(element);
// Now when element goes out of scope,
// both element and onClick will be collected even in old browsers that don't
// handle cycles well.
```

### 3: Out of DOM references

```javascript
var elements = {
    button: document.getElementById('button'),
    image: document.getElementById('image'),
    text: document.getElementById('text')
};

function doStuff() {
    image.src = 'http://some.url/image';
    button.click();
    console.log(text.innerHTML);
    // Much more logic
}

function removeButton() {
    // The button is a direct child of body.
    document.body.removeChild(document.getElementById('button'));

    // At this point, we still have a reference to #button in the global
    // elements dictionary. In other words, the button element is still in
    // memory and cannot be collected by the GC.
}
```

### 4: Closures

```javascript
var theThing = null;
var replaceThing = function () {
  var originalThing = theThing;
  var unused = function () {
    if (originalThing)
      console.log("hi");
  };
  theThing = {
    longStr: new Array(1000000).join('*'),
    someMethod: function () {
      console.log(someMessage);
    }
  };
};
setInterval(replaceThing, 1000);
```

This snippet does one thing: every time `replaceThing` is called, `theThing` gets a new object which contains a big array and a new closure (`someMethod`). At the same time, the variable `unused` holds a closure that has a reference to `originalThing` (`theThing` from the previous call to `replaceThing`). Already somewhat confusing, huh? The important thing is that once a scope is created for closures that are in the same parent scope, that scope is shared. In this case, the scope created for the closure `someMethod` is shared by `unused`. `unused` has a reference to `originalThing`. Even though `unused` is never used, `someMethod` can be used through `theThing`. And as `someMethod` shares the closure scope with `unused`, even though `unused` is never used, its reference to `originalThing` forces it to stay active (prevents its collection). When this snippet is run repeatedly a steady increase in memory usage can be observed. This does not get smaller when the GC runs. In essence, a linked list of closures is created (with its root in the form of the `theThing` variable), and each of these closures' scopes carries an indirect reference to the big array, resulting in a sizable leak.

## Unintuitive behavior of Garbage Collectors

Although Garbage Collectors are convenient they come with their own set of trade-offs. One of those trade-offs is _nondeterminism_. In other words, GCs are unpredictable. It is not usually possible to be certain when a collection will be performed.

Although nondeterminism means one cannot be certain when a collection will be performed, most GC implementations share the common pattern of doing collection passes during allocation. If no allocations are performed, most GCs stay at rest. Consider the following scenario:

1.  A sizable set of allocations is performed.
2.  Most of these elements (or all of them) are marked as unreachable (suppose we null a reference pointing to a cache we no longer need).
3.  No further allocations are performed.

In this scenario, most GCs will not run any further collection passes. In other words, even though there are unreachable references available for collection, these are not claimed by the collector. These are not strictly leaks, but still result in higher-than-usual memory usage.

## Chrome Memory Profiling Tools Overview

Chrome provides a nice set of tools to profile memory usage of JavaScript code. There two essential views related to memory: the _timeline_ view and the _profiles_ view.

### Timeline view

![Google Dev Tools Timeline in Action](https://images.ctfassets.net/23aumh6u8s0i/7LhFm4S1xUTSIisQOb3LoI/92d9b1832c488f89aa0e0f73d3273493/timeline) The timeline view is essential in discovering unusual memory patterns in our code. In case we are looking for big leaks, periodic jumps that do not shrink as much as they grew after a collection are a red flag. In this screenshot we can see what a steady growth of leaked objects can look like. Even after the big collection at the end, the total amount of memory used is higher than at the beginning. Node counts are also higher. These are all signs of leaked DOM nodes somewhere in the code.

### Profiles view

![Google Dev Tools Profiles in Action](https://images.ctfassets.net/23aumh6u8s0i/325v2IWTyW3bRMwF5lL2ln/90e6fb4db397080a8062b77716d80c93/profiles) This is the view you will spend most of the time looking at. The profiles view allows you to get a snapshot and compare snapshots of the memory use of your JavaScript code. It also allows you to record allocations along time. In every result view different types of lists are available, but the most relevant ones for our task are the summary list and the comparison list.

The summary view gives us an overview of the different types of objects allocated and their aggregated size: shallow size (the sum of all objects of a specific type) and retained size (the shallow size plus the size of other objects retained due to this object). It also gives us a notion of how far an object is in relation to its GC root (the distance).

The comparison list gives us the same information but allows us to compare different snapshots. This is specially useful to find leaks.


### Example of investigating a memory leak
https://auth0.com/blog/four-types-of-leaks-in-your-javascript-code-and-how-to-get-rid-of-them/