+++
title = "What are object prototypes? [Part 1]"
description = "Or to be more precise, how can I use them to my advantage and what traps do I need to watch out for?"
tags = []
date = "2017-08-20T07:23:06.422Z"
categories = [
    "JavaScript",
    "Development",
    "Object Oriented Programming",
    "OOP"
]
+++

I'm sure we've all heard somebody say it before, possibly even ourselves, that _'everything in JavaScript is an object'_. Well, that's simply **not true**.

It's my personal belief that some confusion may lie in the fact that we can _apparently_ access properties and call methods on other types of values, such as a `string` or `number`.

```javascript
let name = 'Jacob'; // name is a string

name.length; // 5
name.charAt(2); // 'c'

let age = 31; // age is only a number

age.toExponential(); // '3.1e+1'
age.toString(); // '31'
```

But there is more to this than meets the eye, as we shall come back to see later on.

### Okay, so what are prototypes?

A prototype is an object present on almost all JavaScript objects which - in one sense - can be looked at as a fallback for properties not found directly on objects for which it has been used as a constructor.

That was a mouthful, so let's see a quick example.

```javascript
function Table(cols, rows) {
    this.cols = cols;
    this.rows = rows;
}
Table.prototype.getCols = function() {
    return this.cols;
};
Table.prototype.getRows = function() {
    return this.rows;
};

const results = new Table(7, 4); // Create a new Table "instance"

results.hasOwnProperty('getCols'); // false
results.hasOwnProperty('getRows'); // false

results.getCols(); // 7
results.getRows(); // 4

```

 > A constructor refers to any time you call a function with the `new` keyword. Note, there is **no such thing as a _constructor function_**, only **_constructor calls_**.

Back to the example at hand. If results doesn't have `getCols` or `getRows` as properties on it, what is going on here?

When `getCols` and `getRows` are not found as properties on the `results` object, they are then looked up in the prototype chain, the next point of call being its constructor, `Table`, of which it is considered an _instance_, where they are found and invoked.

As we can see here, the prototype of `results` being `Table`.

```javascript
Object.getPrototypeOf(results); // Table { getCols: [Function], getRows: [Function] }
```

 > This is what people commonly refer to as **_prototypal inheritance_**. But, this is not strictly true. For it to be considered inheritance, the `results` object would have had to inherit the `getCols` and `getRows` methods, with them being present on it.

 > This is actually a form of **_behaviour delegation_**.

But I think we glossed over something entirely before. Something which we just take for granted. The call to `hasOwnProperty()`. This isn't on the `results` object or it's prototype `Table`. So where is it? Where is next up the prototype chain? Let's take a look.

```javascript
Object.getPrototypeOf(Table); // [Function]
```

Yep, the `Function` constructor, but that doesn't contain the `hasOwnPropertyMethod()` method either.

Now, do you want to see something weird?

```javascript
Object.getPrototypeOf(Function); [Function]
```

It looks like we've reached the top of the prototype chain and we still haven't found the `hasOwnProperty()` method. I thought we were gonna keep going up and up until we found it?

Well, I'll let you in on a little secret. This isn't weird at all.

In JavaScript, `Function` is a constructor and the `Function` constructor is one of JavaScript's many _intrinsic objects_.

Let's check out what `Function` is actually an instance of.

```javascript
Function instanceof Object; // true
```

That's right, the big daddy of them all, the `Object` constructor. And - if you haven't forgotten what we were actually looking for - the location of the `hasOwnProperty()` method.

That is a **very simple** overview of how JavaScript's prototype mechanism works. But don't worry, after I go off on this short - but important - tangent, we're going to delve deeper into the prototype mechanism (or up the prototype chain)...or something funny...fuck it...I tried.

### Intrinsic objects

I mentioned briefly that the `Function` constructor was one of JavaScript's intrinsic objects.

What is an intrinsic object? It's just a fancy way of saying that it's a built-in object.

Why do I bring this up? Remember at the beginning of this article we talked about _apparently_ being able to access properties on the `name` string and the `age` number?

`Function` is not JavaScript's only intrinsic object. The full list is:

 - `Array`
 - `Boolean`
 - `Date`
 - `Error`
 - `Function`
 - `Number`
 - `RegExp`
 - `String`

So let's take a quick look at that example again.

```javascript
let name = 'Jacob'; // name is a string

Object.getPrototypeOf(name); // [String: '']


let age = 31; // age is only a number

Object.getPrototypeOf(age); // [Number: 0]
```

So when we enter `name.length` and get `5` as a result, we're not accessing a property on the string at all. JavaScript recognises that we're trying to access a property on a string, and so delegates the request to the intrinsic `String` object's prototype.

It should also be pointed out though, that neither the string or number here were actually created using their relevant constructors.

```javascript
23 instanceof Number; // false
name instanceof String; // false
```

If we had of created them using constructor calls, we would have something very different.

```javascript
let name = new String('Jacob'); // [String: 'Jacob']
```

---

#### Okay, back to the prototypes!

### Overriding and extending prototypes

Remember we have our `Table` constructor and the `getCols()` and `getRows()` methods on it's prototype? I don't, I can't be arsed to scroll back up, and I'm going to add something, so here it is again.

```javascript
function Table(cols, rows) {
    this.cols = cols;
    this.rows = rows;
}
Table.prototype.getCols = function() {
    return this.cols;
};
Table.prototype.getRows = function() {
    return this.rows;
};

const results = new Table(7, 4); // Create a new Table instance

const friendlyResults = new Table(9, 5); // Create another instance of Table
```

When we add a property to an object, it is added directly on the object and is not represented any higher up in the prototype chain. By using the same name as in the prototype, this gives us the ability to override the 'inherited' functionality for that particular object.

This is known as _shadowing_, where the new property on the instance _shadows_ the property of its constructor.

```javascript
friendlyResults.getCols = function() {
    return `I have ${this.cols} columns`;
};

results.getCols(); // 7

friendlyResults.getCols(); // 'I have 9 columns'
```

We can also add additional methods to the base `Table` prototype, which will be picked up by all instances.

```javascript
Table.prototype.getCells = function() {
    return this.rows * this.cols;
}

results.getCells(); // 28
friendlyResults.getCells(); // 45
```

### Borrowing (or inheriting) another object's prototype

Let's say we want another table constructor - `HeadedTable` - this one we want to be able to create tables with a header.

Rather than manually adding the functionality to create and retrieve columns, rows, and cells all over again, we can borrow from our `Table` constructor.

```javascript
function HeadedTable(header, cols, rows) {
    Table.call(this);
    this.header = header;
    this.cols = cols;
    this.rows = rows;
}
Object.setPrototypeOf(HeadedTable.prototype, Table.prototype );;

const tableWithHeader = new HeadedTable('My New Table', 7, 5);

tableWithHeader.getCells(); // 35
```

> This, again, is commonly referred to as inheritance. It is not, it is simply _behaviour delegation_ in action.

We haven't messed with what `HeadedTable` is a prototype of, but we added to its prototype's prototype.

```javascript
Object.getPrototypeOf(HeadedTable); // [Function]

Object.getPrototypeOf(HeadedTable.prototype); // Table
```

Despite this, we can still keep adding to `HeadedTable`'s prototype.

```javascript
HeadedTable.prototype.getHeader = function() {
    return this.header;
}

tableWithHeader.getHeader(); // 'My New Table'
```

If you're still not convinced that there is not some form of inheritance going on, watch closely.

```javascript
Table.prototype.getCells = function() {
    return `Look at my ${this.cols * this.rows} cells`;
}

results.getCells(); // 'Look at my 28 cells'
tableWithHeader.getCells(); // 'Look at my 35 cells'
```

In a classical inheritance model, a change or addition to `Table` after `HeadedTable` has been instantiated would not be reflected on `HeadedTable`. But in JavaScript, because it delegates it's behaviour to the `Table`'s prototype, any changes or additions are accessible via the prototype chain.

This can be a problem. As observed in the previous example, when we call `tableWithHeader.getCells()` we weren't expecting to receive a string like that, we were expecting a number.

Any code further down the line that calls `tableWithHeader.getCells()` and expects a number value to be able to work with is going to be in for a shock. Or to be more precise, throw an error or return `NaN`. Probably not what we were hoping for.

### Part 1: Summary

That concludes **Part 1** of our investigation into object prototypes.

So far we have learnt:

 - Objects have a prototype, essentially a reference to its constructor.
 - Calls to properties on an object are delegated up the prototype chain.
 - Using behaviour delegation to access properties on another object.
 - Extending functionality by creating new constructors.
 - Changing the constructor's prototype affects it's instances.
 - Adding a property with the same name shadows it's constructor's.

Stay tuned for **Part 2**, because the prototype chain doesn't end here, we still have a long way to climb before we truly understand JavaScript's object prototypes.
