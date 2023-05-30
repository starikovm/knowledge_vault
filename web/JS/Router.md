https://medium.com/planet-arkency/one-router-to-route-them-all-or-finding-the-best-routing-766764802f70

It is a [[Cross-cutting concern]]

## I’d like to have control about how router data is passed to my module

There are routing solutions that are created in a way that takes away one of the biggest powers in the OOP world — which is constructing my objects or passing data to it. A solution which needs to be hacked to pass data as props to my React components, or solution which forces me to change interface of my application in a strict way is a no-go for me.

I want to have full control of initialising, passing data or constructing my routed application — please don’t take this opportunity from me. This creates high coupling and defeats the whole purpose of encapsulation. Would you use Redux if you’d be forced to create methods in your React components with strict names? I’d not.

Backbone (or Ampersand) Router makes this right. You can create your own callback function, or even replace the whole route resolving algorithm — you get all route data passed as a callback parameter and you can use whatever style you want to pass this state further.

## I’d like to construct my routes in an arbitrary way

A wide variety of routes comes with a strict _hierarchical_ approach when it comes to defining routes — which I find a perfect way to provide a good convention for routing solution users. In my particular use cases it is not what makes me happy — I often work with legacy applications which often have weird requirements when it comes to their URLs.

Also, while hierarchical approach is good for start, it often falls off later during development — you may want to have applications which breaks the hierarchy from time to time. That said, I’d rather take freedom of defining my routes in a very free way. Ruby on Rails router does this right — it allows you to define your routes in a very conventional way by resource, resources family of methods and also allows you to define your routes in a very simple way like get ‘foo/bar’, controller: …, action: … — so you have the best of two worlds. Not to mention there are great opportunities available with this approach — like creating routing constraints to simplify controllers code — we’ve got an example shown [in our excellent book about working with legacy Rails code](http://rails-refactoring.com/).

## I’d like to have a distinction between _local_ and _global_ routes

It is especially important in a process of transitioning between Page/Widget phase of your application to SPA phase.

In short, a Page/Widget phase is a phase where you don’t have a frontend solution for a whole user interface. Instead, between application modules you have a full page refresh (so your “global” routes are resolved by the backend) and each module is a separate frontend app.

In such applications you often benefit more from _local_ routes. If you have a module under url /projects, you’d most likely have a local route /projects#new which shows you a “New Project” form (possibly as a modal).

If you transition to the single page application phase, you’d benefit more from so-called _global_ routes — so your backend will be only responsible for giving you data, and all routing will be handled in user’s browser. In fact, those global and local routes have often totally different requirements — in global routes it is often needed to fetch data before transitioning, which is a rare case in local routes approach.

The defining local or global routes also should differ — while I find it OK to keep global routes aggregated, I’d like to have local routes defined on the local level — so per module.

I don’t know the solution that provides me such kind of granularity. If you know such solution, let me know :).

## I’d like to have full freedom where I resolve my routes

Some routing solutions are opinionated when it comes to resolving transition phase — they force you to resolve it on the UI adapter layer (so, for example in React) or on the data management level (so in Redux). This is limiting and possibly hurtful for the overall design of an application. A good routing solution is unopinionated where routes should be resolved and how transition should get propagated in the whole module. This is often connected with the first point with passing data — if I can pass data in an arbitrary way.

Here again, Ampersand-Router shines — with a callback approach I can just send an event, or resolve the route on an adapter level in my hexagonal module. It is unopinionated how I do it, or even which module resolves the route — and I like it.

## I’d like to have undo/redo capabilities

Most solutions provide it, yet it is not that obvious — and it is an interesting problem to tackle. There are subtle edge cases to cover like calling after/before callbacks which need to be implemented in a smart way.

## I’d like to have a declarative API — or better, stateless solution

Declarative API is a thing I dream often when it comes to using routing solutions. Publish-subscribe solutions that are a mainstream way to implement routing APIs are fine, but ugly. Not only it is tempting (or worse, it is the only solution) to pollute your codebase with listeners on wrong layers of your application, but also it is a very implicit way.

I like how React-Redux solves the problem of connecting components to Redux stores and I’d like to see the same declarative approach in my ideal routing solution. This way I can define how my app handles routes outside of it — which I find extremely beneficial for testing.

Stateless router would be a great solution because I’d be able to not store it as a singleton — or better, being a singleton wouldn’t be a problem at all. Immutable globals are fine since you can’t break anything with them :).

## I’d like my router to be unobtrusive during testing

Routing, as every cross-cutting concern tends to be painful during testing — you need to be aware that your application is routed and provide an appropriate testing state in your application. Mocking is not a solution here — you will still be aware of routing and need to take it into consideration. An ideal router would be the one which allows to create two totally separate tests — one for application, without _any_ mentions about router and the second one for code responsible for resolving routes — where I don’t need to touch an application and I can just provide an object with the same API.

Points about resolving and passing data are extremely relevant if you want to create such testable solution. Declarativeness and turning the flow inside-out (so no handling routing on your app, but wrapping your app in a code which handles routing — like React-Redux) is a big help here.

## Summary

I don’t think the solution I’ve just described in points exists. I’m not even sure that some points do not exclude each other. Yet, I believe we can do better in terms of routing tooling than we are doing now. I’m also interested in your perspective — do you share my thoughts about the ideal router? What routing solutions are you using and what is the most painful part about it? Maybe I omitted a point you find extremely important? I’m looking forward to your input!