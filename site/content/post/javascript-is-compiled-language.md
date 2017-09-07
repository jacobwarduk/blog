+++
title = "JavaScript is a compiled language?"
description = "You can hear your better judgement telling you \"no, JavaScript is an interpreted language\". But, I can assure you it's not, and today we're going to prove it."
tags = []
date = "2017-09-07T18:09:27.747Z"
categories = [
    "JavaScript",
    "Development",
    "Computer Science"
]
+++

This post stems from a little spat I had with another developer in the comment section of their blog. They assured me that "JavaScript is an interpreted language" and "just because we have all these tools and frameworks which mean we have to 'compile' it doesn't make it a compiled language".

Well, for a start he obviously doesn't understand the meaning of _compile_. All of "these tools and frameworks" don't mean that we have to "compile" anything, usually it is _transpiling_ from one superset language (e.g. Typescript), or the latest versions of the ECMAScript standard, down to something that vaguely resembles ES5.

But, I understand what he meant, as at the end he pointed out the glaring difference between JavaScript and other languages, such as Java and C#, if I remember correctly. To this, I just pointed out that those languages have a different distribution method than JavaScript.

### Distribution of interpreted vs. compiled applications

I believe this is where the main point of contention (or confusion) lies (though it shouldn't, and we will see why later).

There seems to be a common misconception that if our application is shipped to the End User as source code, that when it's ran, it is being interpreted by another application.

```
~ $ php app.php  // app.php being interpreted by PHP

~ $ python app.py  // app.py being interpreted by Python
```

With both PHP and Python actually being interpreted languages, this is indeed the case.

Compare that to the common view of compiled applications. We sit there and run our code through the compiler...let a few {minutes|hours|days} pass...and eventually we have a binary application that we can ship to any user to open and use on their machine.

```
C:\> app.exe  // Running a Windows executable

~ $ ./app.bin  // Running a binary file
```

#### So, where does JavaScript sit on this apparent fence?

 1. We ship it as a source code file, such as `app.js`
 1. The End User opens it in their browser or Node.js
 1. The application is ran

It sounds a lot like an interpreted language, doesn't it?

Well, there's a whole lot more going on between steps 2 and 3.

It has got nothing to do with being interpreted, and everything to do with being compiled.

### The JavaScript engine

There are a number of popular JavaScript engines in use today, though I have chosen only to name three here.

There are two reasons for this. The first one being that they are currently the most up-to-date with modern JavaScript standards, or at least are attempting to be. The second being that all of the companies behind them also have members with voting rights on the [TC39 committee](http://www.ecma-international.org/memento/TC39.htm), which is the body that standardises the [ECMAScript (JavaScript) specification](https://tc39.github.io/ecma262/).

 - **[V8](https://developers.google.com/v8/)** is developed by Google and used in its Chrome browser. Under the hood, Node.js also uses an implementation of the V8 engine.
 - **[Chakra](https://github.com/Microsoft/ChakraCore)** is developed by Microsoft and used in its Edge browser. It is also possible to use ChakraCore as the Node.js engine rather than V8, should one choose to.
 - **[SpiderMonkey](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey)** is developed by Mozilla and is used in its Firefox browser. It can also boast the claim of being the first ever JavaScript engine implementation, way back when Netscape Navigator was fighting for market share with IE.

Though these engines may have different implementations, they all follow the ECMAScript specification which gets our `app.js` application from JavaScript source code, to executable machine code.

 > Yes, compiled to executable machine code. Not interpreted.

This is then executed by the host environment, such as a web browser or Node.js.


### The JavaScript compilation process

So, if JavaScript is indeed being compiled and not interpreted line-by-line by another application, what exactly is going on inside the JavaScript engine?

I we take the following simple JavaScript application.

```javascript
let x = 1 + 2;
```

It's not the most interesting, or even useful, of programs. However, it is perfect for demonstrating how the JavaScript engine turns JavaScript into executable code.

#### 1. Lexing & Tokenisation

The lexing process, or _lexical analysis_, takes our JavaScript code as input and converts it into a series of strings.

These strings each have a unique identifiable meaning which will be used during _tokenisation_, where you guessed it... they will be turned into tokens.

A lexical analysis of our application would break our code up - from left to right - into its constituent parts: `let`, `x`, `=`, `1`, `+`, `2`, `;`.

If these constituent parts have meaning, according the the [Lexical Grammar of the ECMAScript specification](https://tc39.github.io/ecma262/#sec-ecmascript-language-lexical-grammar), they will then be appropriately tokenised. (n.b. I have deliberately ignored whitespace in our example as it has no grammatical meaning in this context).

`let` would be considered a _LetOrConst_ or a _VariableDeclaration_, while `x` an _Identifier_. `=` is an _AssignmentExpression_. Both `1` and `2` are _Literal_ and `+` is (in this case) a _BinaryExpression_.

These tokens are all streamed (as an array) to the parser.

#### 2. Parsing to an Abstract Syntax Tree (AST)

The tokens extracted from the stream during the lexing & tokenising process are used to form a tree of nested elements.

```javascript
{
    "type": "Program",
    "body": [
        {
            "type": "VariableDeclaration",
            "declarations": [
                {
                    "type": "VariableDeclarator",
                    "id": {
                        "type": "Identifier",
                        "name": "x"
                    },
                    "init": {
                        "type": "BinaryExpression",
                        "operator": "+",
                        "left": {
                            "type": "Literal",
                            "value": 1,
                            "raw": "1"
                        },
                        "right": {
                            "type": "Literal",
                            "value": 2,
                            "raw": "2"
                        }
                    }
                }
            ],
            "kind": "let"
        }
    ],
    "sourceType": "script"
}
```

This tree, an _Abstract Syntax Tree_ (AST), represents the grammatical structure of our program.

This is merely a brief overview. If you really want to see our code's AST in detail check out this [snippet on AST Explorer](https://astexplorer.net/#/gist/1dd8beabec48f8b100abbc6b954f74b2/e0781e9578608ca4259c464c869dcbd56cd21002).

#### 3. Translating (Compiling) to byte code

With the AST essentially representing what we want our code to do. This is then translated, or _**compiled**_ to byte code by the JavaScript engine's compiler.

The compiler will take at least one pass over the AST, some JavaScript compilers take many more passes, depending on the operations they perform.

Many modern compilers support operations such as _intermediate representation_, _code optimisation_ of loops and common algorithmns, _JIT_ (Just in Time) compilation, and even (hot) _recompilation_ during execution.

Compiler theory is well beyond the scope of this short blog post, but it's an interesting topic. I suggest you go check it out!

**_Voila!_**

The bytecode generated from our JavaScript application through the process of **compilation** is now ready to be executed.

At this point we get into _how_ the code is executed, which I believe is a topic for another post entirely.

