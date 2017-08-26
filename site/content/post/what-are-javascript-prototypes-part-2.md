+++
title = "What are object prototypes? [Part 2]"
description = "Continuing on from Part 1, we delve deeper into discovering the mechanics behind object prototypes."
tags = []
date = "2017-08-21T18:46:43.467Z"
categories = [
    "JavaScript",
    "Development",
    "Object Oriented Programming",
    "OOP"
]
draft = true
+++

Let's briefly recap what we learnt about [JavaScript object prototypes in Part 1](http://jacobward.io/post/what-are-javascript-prototypes-part-1/). We learnt that objects have a prototype which is it's link to other objects in the prototype chain. We learnt about _constructor calls_, _behaviour delegation_, _intrinsic objects_, and concepts that in Classical Inheritance Land might be called "classes", "inheritance", and "polymorphism".

Today we're going to learn more about how objects are related by their prototype, and alternative ways of thinking about linking objects that don't conform to the 'class' style of Object Oritented Programming (OOP) previously mentioned, that many people may be more familiar with.


### Inspecting an object's prototype

Previously, to inspect an object's prototype chain we were using `Object.getPrototypeOf`, and working our way up the chain.

```javascript
Object.getPrototypeOf(results); // Table { getCols: [Function], getRows: [Function] }

Object.getPrototypeOf(Table); // [Function]

Object.getPrototypeOf(Function); [Function]
```

Fortunately for us, browsers long ago started to implement what was - at the time - a non-standard way of accessing an object's prototype - `__proto__`. This was then standardised in ES6 (ES2015), and is also available in Node.js.

```javascript
results.__proto__; // Table { getCols: [Function], getRows: [Function], getCells: [Function] }
```

Want to know what's really awesome though? These are chainable.

```javascript
tableWithHeader.__proto__.__proto__; // Table { getCols: [Function], getRows: [Function], getCells: [Function] }

tableWithHeader.__proto__.__proto__.__proto__.constructor // [Function: Object]
```

This makes it a lot easier to inspect the prototype chain of any given object.

But don't be fooled into thinking that the `__proto__` property exists on the object. Remember when we looked at intrinsic objects, and how trying to access a property on a string inspected the intrinsic `String` object's prototype? The same thing is happening here with the objects. The `__proto__` property lives on the intrinsic `Object` object's prototype.


### Changing an object's prototype

It is possible to change the prototype of an existing object, should we feel the need to, but it is [strongly discouraged](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf).

If, however, you're like me and from time-to-time enjoy doing stupid shit just for the sake of stupid shit, then it's pretty easy. We just need two objects.

```javascript
const personality = {
    behaviour: 'stupid'
};

const me = {
    likes() {
        console.log(`I like to do ${this.behaviour} shit`);
    }
};

Object.setPrototypeOf(me, personality);

me.likes(); // I like to do stupid shit
```

### Avoiding an object's prototype
It's not unheard of (it's happened to me at least) that in dealing with an object, we accidentally access properties further up the prototype chain. Usually ones we never knew existed, let alone wanted to retrieve.

This is a common occurrence when iterating over the properties of an object.




### Conclusion
I'm sure there's plenty more to JavaScript object prototypes that I haven't covered here. When I discover them I'm sure I'll update. Until then, prototype away...

