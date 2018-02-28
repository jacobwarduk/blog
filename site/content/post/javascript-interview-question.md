+++
title = "The essential JavaScript interview question"
description = "How what may seem like such a simple JavaScript interview question can reveal so much of a candidates understanding of the language."
tags = []
date = "2018-02-28T22:19:20.063Z"
categories = [
    "JavaScript"
]
+++

![JavaScript Interview Questions Masthead](/images/javascript-interview-questions.jpg)

After having contracted for sometime now, I've had the opportunity to sit in on a number of interviews - mostly for developers to replace myself!

But, having recently accepted a permanent role as Lead Developer at [All Fleet Online](https://www.allfleetonline.com), it's now part of my role to interview potential candidates for the junior roles we have in our expanding team.

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
 - The Call Stack
 - The Message Bus
 - The `Number` type (IEEE 754)
 - Side effects
 - Functional programming

## Having the candidate walk through the problem with you.

This is where the magic happens. If you don't walk through it with them - explaining where there may be gaps in their knowledge and asking them to expand on certain concepts - the exercise is worthless.

I will include links along the way to related materials for those that may not be familiar with terms or concepts explored in this simple, but also complex, piece of code.

---

```javascript
(function() { .. })();
```

We have a function wrapped in parentheses. Since parentheses cannot contain a _statement_, the JavaScript engine knows that what is inside needs to be evaluated as an _expression_. As such, we have a _function expression_ and **not** a _function declaration_.

On the last line we also have both opening and closing parentheses `()`, indicating that an _invocation_ should take place.

Since we are immediatly invoking a function expression, we have an **Immediately Invoked Function Expression** or **IIFE** for short.

Read: [Ben Alman &raquo; Immediately-Invoked Function Expression (IIFE)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)

---

Now we are inside of an executing function, we have created a new function _execution context_ and _pushed_ it onto the _call stack_.

Read: [What is the Execution Context &amp; Stack in JavaScript? by David Shariff](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)

---

```javascript
const numbers = [0.1, 0.2, 0.3];
```

Inside of our IIFE, on the first line we have a single equals sign `=`, indicating an _assignment_ should take place.

The right hand side of the assignment operator is an _array_ containing three numerical values.

The left hand side is the variable name `numbers`, to which we are assigning the array.

We are assigning the array to the pointer `numbers` using the `const` keyword.

The `const` keyword indicates that once this assignment has taken place, the `numbers` variable name cannot be reassigned.

If this had been assigned to an _immutable_ value, such as the number `23` or the string `'hello world'`, then will **always** return that value. Since it has been assigned to a _mutable_ value (an array), whilst it cannot be reassigned and will always return that particular array, we can still change the values contained in the array, e.g. `numbers.push('apple');`, giving the return value that of `[0.1, 0.2, 0.3, 'apple']`.

Read: [JavaScript variable assignment explained - Syntax Success](http://www.syntaxsuccess.com/viewarticle/javascript-variable-assignment-explained)

Read: [Understanding let and const in JavaScript ES6 - JavaScript Kit](http://www.javascriptkit.com/javatutors/javascript-es6-let-const.shtml)

---

```javascript
let sum = 0;
```

The next line is again, an assignment. This time the right hand side is the number `0` and the variable name on the left `sum`.

We are using the `let` keyword to make this assignment. Whilst there are some idiosyncrasities involved when using the `let` keyword, we can think of it as being a block-scoped variable that cannot be redeclared, but can be reassigned.

Meaning that later on in our code, we can do `sum = 23` and `sum` will now point to `23`.

Read: [Understanding let and const in JavaScript ES6 - JavaScript Kit](http://www.javascriptkit.com/javatutors/javascript-es6-let-const.shtml)

---

```javascript
for (var i = 0; i < numbers.length; ++i) { .. }
```
We now have the opening of a `for` loop and its header.

Inside the header we make another assignment, this time initialising the value of `0` to the variable `i` using the `var` keyword. Unlike `let` and `const`, the `var` keyword is not block scoped, it is function scoped. This means that it is scoped to its nearest outer function, in this case the IIFE, and current execution context we are in.

Next is the condition for the `for` loop, where we say that we want the loop to continue executing until the value that `i` points to is less than (`<`) the value of `numbers.length` (the length of the numbers array.

Lastly, for each time the loop executes we increment the value that `i` points to by one.

Read: [Getting Fancy with the JavaScript For Loop - HTML Goodies](https://www.htmlgoodies.com/html5/javascript/getting-fancy-with-the-javascript-for-loop.html)

---

```javascript
setTimeout(() => { .. }, 0);
```

Now we are inside of the `for` loop and the first thing we do is use the browser (not JavaScript!) function `setTimeout` contained on the `window` or _global_ object.

`setTimeout` is one of the timing functions. It is passed two arguments: A callback function to execute, and the delay (in milliseconds) before that function should be invoked.




Read: [How JavaScript Timers Work - Jon Resig](https://johnresig.com/blog/how-javascript-timers-work/)




---

There are a number of ways to solve this problem. If you've solved it in a different way,  [I'd love to hear about it](https://github.com/jacobwarduk/jacobward.io/issues/new).
