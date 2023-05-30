https://css-tricks.com/animating-layouts-with-the-flip-technique/

-   **First:** before anything happens, record the current (i.e., first) position and dimensions of the element that will transition. You can use `element.getBoundingClientRect()` for this, as will be shown below.
-   **Last:** execute the code that causes the transition to instantaneously happen, and record the final (i.e., last) position and dimensions of the element.*
-   **Invert:** since the element is in the last position, we want to create the illusion that it’s in the first position, by using `transform` to modify its position and dimensions. This takes a little math, but it’s not too difficult.
-   **Play:** with the element inverted (and pretending to be in the first position), we can move it back to its last position by setting its `transform` to `none`.

Code exmaple:
```javascript
const elm = document.querySelector('.some-element');

// First: get the current bounds
const first = elm.getBoundingClientRect();

// execute the script that causes layout change
doSomething();

// Last: get the final bounds
const last = elm.getBoundingClientRect();

// Invert: determine the delta between the 
// first and last bounds to invert the element
const deltaX = first.left - last.left;
const deltaY = first.top - last.top;
const deltaW = first.width / last.width;
const deltaH = first.height / last.height;

// Play: animate the final element from its first bounds
// to its last bounds (which is no transform)
elm.animate([{
  transformOrigin: 'top left',
  transform: `
    translate(${deltaX}px, ${deltaY}px)
    scale(${deltaW}, ${deltaH})
  `
}, {
  transformOrigin: 'top left',
  transform: 'none'
}], {
  duration: 300,
  easing: 'ease-in-out',
  fill: 'both'
});
```

 - If the element’s size changed, you can transform `scale` in order to “resize” it with no performance penalty; however, make sure to set `transformOrigin` to `'top left'` since that’s where we based our delta calculations.

# Shared element transition
In case end of transform should be a different element:
```javascript
const firstElm = document.querySelector('.first-element');

// First: get the bounds and then hide the element (if necessary)
const first = firstElm.getBoundingClientRect();
firstElm.style.setProperty('visibility', 'hidden');

// execute the script that causes view change
doSomething();

// Last: get the bounds of the element that just appeared
const lastElm = document.querySelector('.last-element');
const last = lastElm.getBoundingClientRect();

// continue with the other steps, just as before.
// remember: you're animating the lastElm, not the firstElm.
```

# Parent-child transition
```javascript
const parentElm = document.querySelector('.parent');
const childElm = document.querySelector('.parent &gt; .child');

// First: parent and child
const parentFirst = parentElm.getBoundingClientRect();
const childFirst = childElm.getBoundingClientRect();

doSomething();

// Last: parent and child
const parentLast = parentElm.getBoundingClientRect();
const childLast = childElm.getBoundingClientRect();

// Invert: parent
const parentDeltaX = parentFirst.left - parentLast.left;
const parentDeltaY = parentFirst.top - parentLast.top;

// Invert: child relative to parent
const childDeltaX = (childFirst.left - parentFirst.left)
  - (childLast.left - parentLast.left);
const childDeltaY = (childFirst.top - parentFirst.top)
  - (childLast.top - parentLast.top);
  
// Play: using the WAAPI
parentElm.animate([
  { transform: `translate(${parentDeltaX}px, ${parentDeltaY}px)` },
  { transform: 'none' }
], { duration: 300, easing: 'ease-in-out' });

childElm.animate([
  { transform: `translate(${childDeltaX}px, ${childDeltaY}px)` },
  { transform: 'none' }
], { duration: 300, easing: 'ease-in-out' });
```
-   Changing dimensions in parent and/or child (`width`, `height`) was purposefully omitted in this example, since it is an advanced and complex topic. Let’s save that for another tutorial.
-   You can combine the shared element and parent-child techniques for greater flexibility.