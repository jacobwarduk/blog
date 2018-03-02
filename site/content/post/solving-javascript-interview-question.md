+++
title = "Solving the essential JavaScript interview question"
description = "Exploring some potential solutions to the essential JavaScript Interview question and some further reading."
tags = []
date = "2018-03-02T12:40:21.835Z"
categories = [
    "JavaScript"
]
+++

![JavaScript Interview Solutions Masthead](/images/javascript-interview-solutions.jpg)

 > This is **Part 2** of **[Presenting the essential JavaScript interview question](http://jacobward.io/post/presenting-javascript-interview-question/)**

Just to remind ourselves of the problem at hand, here's the code again for reference.

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


There are actually two problems that need to be solved in this exercise.

---

## Problem 1

We need to sum the values in the `numbers` array and assign that value to the `sum` variable.

There are a number of ways we can accomplish this. I'll just cover a couple and point you in the right direction for others, otherwise we could be here for days!

<br />

### 1. Remove the `setTimeout`

By removing the `setTimeout(() => { .. }, 0);`, the enclosed operation takes place immediately inside the current execution context, rather than being added to the message queue.

This means that `sum += numbers[i];` has access to the `i` variable each time that it is incremented and retrieves each item from the `numbers` array.

Thus `sum` becomes the sum of each item in the `numbers` array.

```javascript
(function() {
    const numbers = [0.1, 0.2, 0.3];
    let sum = 0;

    for (var i = 0; i < numbers.length; ++i) {
        sum += numbers[i];
    }

    console.log(sum === 0.6);
})();
```

<br />

### 2. Use `Array.prototype.reduce`

This is a classic _reducer_ pattern, so what better way to solve it than to use `reduce`?

This functionality exists in all popular _functional programming_ libraries for JavaScript, such as [Lodash](https://lodash.com/docs/4.17.5#reduce) or [Underscore](http://underscorejs.org/#reduce).

It's also available directly on the `Array` prototype in JavaScript!

```javascript
(function() {
    const numbers = [0.1, 0.2, 0.3];

    let sum = numbers.reduce((a, c) => a + c);

    console.log(sum === 0.6);
})();
```

Read: [Array.prototype.reduce() - MDN web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

### Other potential solutions

 - Create an _implicit closure_ by initialising `i` with `let`.
 - Create an _explicit closure_ by wrapping the `setTimeout` in an IIFE.

---

## Problem 2

You may have noticed that even after implementing either of the previous solutions, we still have the problem that our assertion `console.log(sum === 0.6)` returns `false`.

This is due to the standard that JavaScript uses for its `Number` type - [IEEE Standard 754](http://steve.hollasch.net/cgindex/coding/ieeefloat.html).

This does not actually store the number we have, but a floating point representation of that number. This makes any assertion we make unreliable.

More often than not, the number we actually end up with after summing `0.1`, `0.2`, and `0.3`, is actually `0.6000000000000001`. This is _pretty close_ to `0.6` but equality doesn't deal with _pretty close_, it deals with being _equal_.

So, how can we fix this? Again, I'll cover a couple of solutions then point you in the right direction for others.

I'll use *Solution 2* from above as the basis for these solutions, since _I believe_ this is the best solution for the first problem.

<br />

### 1. Use `Number.prototype.toFixed`

Since we know that we're evaluating the value pointed to by `sum` against `0.6` - a number to one decimal place - we can use `toFixed` to ensure that the value `sum` points to is to one decimal place too.

```javascript
(function() {
    const numbers = [0.1, 0.2, 0.3];

    let sum = numbers.reduce((a, c) => a + c).toFixed(1);

    console.log(sum === 0.6);
})();
```

 > Returns `false` ...huh, why didn't that work? 🤔

Well, illogical as it may sound, `Number.prototype.toFixed` doesn't actually return a value of the `Number` type, it returns a string representation of that number.

So, we then have to parse that string back into the `Number` type to get a number. For this purpose, we'll use `Number.parseFloat`.

 > As a side note, `parseFloat` is actually available on the global (or `window`) object, but was added as a static method to the `Number` object in ES6 (ES2015).

```javascript
(function() {
    const numbers = [0.1, 0.2, 0.3];

    let sum = Number.parseFloat(numbers.reduce((a, c) => a + c).toFixed(1));

    console.log(sum === 0.6);
})();
```

 > Returns `true` ...happy days! 😁

<br />

### 2. Use `Math.round` or `Math.floor` or `Math.ceil` or `Math.`...?

While integers in JavaScript are also represented using floating point, as specified by the IEEE Standard 754, they are **a lot** more reliable (at least small ones are).

So another approach may be to multiply each number by, say 10, then _floor_ it, then divide by 10 afterwards, e.g.

```javascript
(function() {
    const numbers = [0.1, 0.2, 0.3];

    let sum = numbers
        .map(number => Math.floor(number * 10))
        .reduce((a, c) => a + c) / 10;

    console.log(sum === 0.6);
})();
```


---

## Conclusion

I hope that after these last two posts about this simple interview exercise, I've managed to convince the naysayers or at the very least, enlightened somebody to a concept they had not previously been aware of.

Hefty hopes and high goals, I know. But you know what they say... well, you probably do, I don't, so...

**[ insert appropriate quote here ]**

---

As previously mentioned, there are numerous ways to solve this problem. If solved it differently then [I'd love to hear about it](https://github.com/jacobwarduk/jacobward.io/issues/new).
