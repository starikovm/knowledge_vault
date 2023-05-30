Articles:
https://pranay.info/2014/07/02/what-every-frontend-developer-should-know-about-webpage-rendering/
https://aerotwist.com/blog/pixels-are-expensive/
https://www.html5rocks.com/en/tutorials/speed/high-performance-animations/

Course:
https://www.udacity.com/course/browser-rendering-optimization--ud860

WEB Animations API
http://danielcwilson.com/blog/2015/07/animations-intro/


## Process of rendering
1) HTML -> DOM
CSS -> CSSOM (Css object model)

2) HTML + CSSOM -> Rendering tree 
 - represents visible elements of a page
 
 3) Calculate(rendering tree) -> **layout**
  - browser calculates coordinates of each element of rendering tree (It's layout)
  - browser optimises prosess and **uses one pass to calculate layout of all elements**
  - for tables weveral passes are required
  - Once the browser knows which rules apply to an element it can begin to calculate how much space it takes up and where it is on screen. The web’s layout model means that one element can affect others, for example the width of the `<body>` element typically affects its children’s widths and so on all the way up and down the tree, so the process can be quite involved for the browser.

4) Browser use calculated layout to **Paint**. Painting is the process of filling in pixels. It involves drawing out text, colors, images, borders, and shadows, essentially every visual part of the elements. The drawing is typically done onto multiple surfaces, often called layers.
6) **Compositing**. Since the parts of the page were drawn into potentially multiple layers they need to be drawn to the screen in the correct order so that the page renders correctly. This is especially important for elements that overlap another, since a mistake could result in one element appearing over the top of another incorrectly.
7) When user interacts with page, browser neets to [[Repaint]] or [[Reflow]]

## How browsers optimise rerendering

 1) restrict repaint/reflow to the area that covers the changed elements only
 -  size change in an absolute/fixed positioned element only affects the element itself and its descendants
 - similar change in a statically positioned element triggers reflow for all the subsequent elements.

2) while running pieces of JavaScript code, browsers cache the changes, and apply them in a single pass after the code was run
- accessing an element property triggers a **forced** [[Reflow]]
- http://blog.wilsonpage.co.uk/preventing-layout-thrashing/


## Optimisations
 
 1) Use `transform` that triggers only **composite step, without layout or paint**. https://www.html5rocks.com/en/tutorials/speed/high-performance-animations/. We need to set element attribute `will-change`, so browser will put it on different composition layer. Alternative old hacks:
  - `-webkit-backface-visibility: hidden` 
  - `-webkit-transform: translateZ(0);`
  - Common way to animate elements is using [[FLIP]]
 3) Simplify and optimize CSS selectors (*For the most part optimizing selector matching is going to give minimal returns.*). This is how CSS selectors rank according to their performance (starting from the fastest ones):  
 - Identificator: #id  
 - Class: .class  
 - Tag: div  
 - Adjacent sibling selector: a + i  
 - Parent selector: ul > li  
 - Universal selector: *  
 - Attribute selector: input[type=”text”]
2) Browsers process selectors from right to left, that’s why the rightmost selector should be the fastest one
3) Minimize DOM manipulation whenever possible. Cache everything, including properties and objects (if they are to be reused). It’s better to work with an “offline” element when performing complicated manipulations (an “offline” element is one that is disconnected from DOM and only stored in memory), and append it to DOM afterwards. [[Virtual DOM]]
4) Animate only absolute/fixed positioned elements if you can.
5) Use `requestAnumationFrame` to put all rendering in next frame rendering alltogether