+++
title = "Presenting the essential JavaScript interview question"
description = "What may seem like such a simple JavaScript interview question can reveal so much of a candidates understanding of the language."
tags = []
date = "2018-02-28T22:19:20.063Z"
categories = [
    "JavaScript"
]
+++

![JavaScript Interview Questions Masthead](/images/javascript-interview-questions.jpg)

After having contracted for sometime now, I've had the opportunity to sit in on a number of interviews - mostly for developers to replace myself!

But, having recently accepted a permanent role as Lead Developer, it's now part of my role to interview potential candidates for the junior roles we have in our expanding team.

As such, I've put together a series of practical 'problems' or 'exercises'. When interviewing for junior roles, I've found it's much easier for a potential candidate to demonstrate knowledge of concepts like _closure_, _scope_, _prototype delegation_, etc... than it may be for them to verbally articulate it. After all, they are interviewing for a junior role with the intention of growing their knowledge working with the rest of the team.

The first exercise I put forward is always the same, as I will show in just a moment, and determines which ones will follow.

I recently posted this on a private message board to get some feedback from other developers - hoping for some constructive criticism. Some was positive, with people trying to explain and solve the problem. A lot was negative, saying that it was a ridiculous question that was contrived and had no practical application. That was, until I explained _how_ and _why_ I always ask it.

## 🥁 Drum roll please for the _problem_ at hand...

```javascript
(function() {
    const numbers = [0.1, 0.2, 0.3];
    let sum = 0;

    for (var i = 0; i < numbers.length; ++i) {
        setTimeout(() => {
            sum += numbers[i];
        }, 0);
    }

    console.log(sum === 0.6);
})();
```

I have almost no doubt in my mind that many of you reading this will have seen something similar before...and yes, it may seem like a garbage question at first. And, if used in the wrong sitting, it is pretty much meaningless and you will derive nothing of a candidate's understanding of JavaScript from it.

 > But, if you will indulge me, I will demonstrate just how valuable this few lines of code actually is in determining an understanding of:

 - Variable assignment operators
 - Scope and Closure
 - The Event Loop
 - The Call Stack
 - The Message Bus
 - The `Number` type (IEEE 754)
 - Side effects
 - Functional programming

## Having the candidate walk through the problem with you.

This is where the magic happens. If you don't walk through it with them - explaining where there may be gaps in their knowledge and asking them to expand on certain concepts - the exercise is worthless.

I will include links along the way to related materials for those that may not be familiar with terms or concepts explored in this simple, but also complex, piece of code.

---

### The Event Loop

This piece of code is opened in a browser and as the _event loop_ ticks over, execution begins...

Read: [The JavaScript Event Loop: Explained - Carbon Five](https://blog.carbonfive.com/2013/10/27/the-javascript-event-loop-explained/)

---

### Function Expressions & IIFEs

```javascript
(function() { .. })();
```

We have a function wrapped in parentheses. Since parentheses cannot contain a _statement_, the JavaScript engine knows that what is inside needs to be evaluated as an _expression_. As such, we have a _function expression_ and **not** a _function declaration_.

On the last line we also have both opening and closing parentheses `()`, indicating that an _invocation_ should take place.

Since we are immediatly invoking a function expression, we have an **Immediately Invoked Function Expression** or **IIFE** for short.

Read: [Ben Alman &raquo; Immediately-Invoked Function Expression (IIFE)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)

---

### Execution Contexts & The Call Stack

Now we are inside of an executing function, we have created a new function _execution context_ and _pushed_ it onto the _call stack_.

Read: [What is the Execution Context &amp; Stack in JavaScript? by David Shariff](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)

---

### Variable Assignment & Identifiers

```javascript
const numbers = [0.1, 0.2, 0.3];
```

Inside of our IIFE, on the first line we have a single equals sign `=`, indicating an _assignment_ should take place.

The right hand side of the assignment operator is an _array_ containing three numerical values.

The left hand side is the variable name `numbers`, to which we are assigning the array.

We are assigning the array to the pointer `numbers` using the `const` keyword.

The `const` keyword indicates that once this assignment has taken place, the `numbers` variable name cannot be reassigned.

If this had been assigned to an _immutable_ value, such as the number `23` or the string `'hello world'`, then will **always** return that value. Since it has been assigned to a _mutable_ value (an array), whilst it cannot be reassigned and will always return that particular array, we can still change the values contained in the array, e.g. `numbers.push('apple');`, giving the return value that of `[0.1, 0.2, 0.3, 'apple']`.

<br />

```javascript
let sum = 0;
```

The next line is again, an assignment. This time the right hand side is the number `0` and the variable name on the left `sum`.

We are using the `let` keyword to make this assignment. Whilst there are some idiosyncrasities involved when using the `let` keyword, we can think of it as being a block-scoped variable that cannot be redeclared, but can be reassigned.

Meaning that later on in our code, we can do `sum = 23` and `sum` will now point to `23`.

Read: [JavaScript variable assignment explained - Syntax Success](http://www.syntaxsuccess.com/viewarticle/javascript-variable-assignment-explained)

Read: [Understanding let and const in JavaScript ES6 - JavaScript Kit](http://www.javascriptkit.com/javatutors/javascript-es6-let-const.shtml)

Read: [var, let, or const? - Hacker Noon](https://hackernoon.com/js-var-let-or-const-67e51dbb716f)

-----

### Loops & Side Effects

```javascript
for (var i = 0; i < numbers.length; ++i) { .. }
```
We now have the opening of a `for` loop and its header.

Inside the header we make another assignment, this time initialising the value of `0` to the variable `i` using the `var` keyword. Unlike `let` and `const`, the `var` keyword is not block scoped, it is function scoped. This means that it is scoped to its nearest outer function, in this case the IIFE, and current execution context we are in.

Next is the condition for the `for` loop, where we say that we want the loop to continue executing until the value that `i` points to is less than (`<`) the value of `numbers.length` (the length of the numbers array.

Lastly, for each time the loop executes we increment the value that `i` points to by one.

Note, that the value of `i` we are incrementing does not exist within the `for` loop. It exists in the execution context of the outer function. As such, this incrementation is a _side effect_ of running the loop.

Read: [Getting Fancy with the JavaScript For Loop - HTML Goodies](https://www.htmlgoodies.com/html5/javascript/getting-fancy-with-the-javascript-for-loop.html)

---

### Global Functions & The Message Queue

```javascript
setTimeout(() => { .. }, 0);
```

Now we are inside of the `for` loop and the first thing we do is use the browser (not JavaScript!) function `setTimeout` which is present on the `window` or _global_ object.

`setTimeout` is a timing function. It is passed two arguments: A _callback_ function to execute, and the delay (in milliseconds) before that function should be invoked.

When `setTimeout` is invoked, the callback function is placed onto the _message queue_, along with the minimum delay after which it should be invoked, in this case `0` milliseconds.

Note that the callback function is not yet invoked, as we are still in our current execution context of the `for` loop, and messages on the queue will not be received until the call stack is empty.

Following the logic of the loop, this happens another two times.

Read: [How JavaScript Timers Work - Jon Resig](https://johnresig.com/blog/how-javascript-timers-work/)

---

```javascript
console.log(sum === 0.6);
```

Since nothing has yet happened to change the value that `sum` was assigned (`0`), this will print `false` in the console, as `0` does not equal `0.6`.

---

### Callbacks, Scope & Closure

```javascript
sum += numbers[i];
```

Now our current execution context has exited and been _popped_ off the call stack, the event loop finally gets around to receiving the functions we added to the message queue.

It invokes each one in turn, creating a new execution context for each, pushing and popping it, on to and off of the call stack.

At this point in time, `i` now points to the value `3`, as the for loop has executed three times pre-incrementing `i` each time.

Since there isn't an array index of `3` in the `numbers` array, `numbers[i]` returns `undefined`.

The first callback function received from the message queue tries to add `0` to `undefined`, which results in `NaN`. The next two functions received from the message queue try to add `undefined` to `NaN`, which again results in `NaN`.

Ultimately leaving `sum` assigned a value of `NaN`.

---

## Identifying and solving the problems

This will be covered in my next post [Solving the essential JavaScript interview question](#), coming next... 🙂

There are a numerous ways to solve this problem. If you've already done it,  [I'd love to hear about it](https://github.com/jacobwarduk/jacobward.io/issues/new).

---

#### In the mean time...

If you're bored in the mean time and find yourself with a half hour spare, this talk by Philip Roberts from JSConf EU 2014 on the JavaScript Event Loop is particularly good watch.

{{< youtube 8aGhZQkoFbQ >}}

---

#### All the links in once place

 - [The JavaScript Event Loop: Explained - Carbon Five](https://blog.carbonfive.com/2013/10/27/the-javascript-event-loop-explained/)
 - [Ben Alman &raquo; Immediately-Invoked Function Expression (IIFE)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
 - [What is the Execution Context &amp; Stack in JavaScript? by David Shariff](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)
 - [JavaScript variable assignment explained - Syntax Success](http://www.syntaxsuccess.com/viewarticle/javascript-variable-assignment-explained)
 - [Understanding let and const in JavaScript ES6 - JavaScript Kit](http://www.javascriptkit.com/javatutors/javascript-es6-let-const.shtml)
 - [var, let, or const? - Hacker Noon](https://hackernoon.com/js-var-let-or-const-67e51dbb716f)
 - [Getting Fancy with the JavaScript For Loop - HTML Goodies](https://www.htmlgoodies.com/html5/javascript/getting-fancy-with-the-javascript-for-loop.html)
 - [How JavaScript Timers Work - Jon Resig](https://johnresig.com/blog/how-javascript-timers-work/)
