+++
title = "The ultimate front end build process?"
description = "After some degree of experimentation and research, I present the ultimate front end development build and deploy process for 'framework-free' (vanilla JavaScript) projects."
tags = []
date = "2017-08-30T18:10:53.734Z"
categories = [
    "JavaScript",
    "Development",
    "Architecture",
    "Front End Ops"
]

draft = "true"
+++

[Webpack](https://webpack.js.org/) may be the new kid on the block, but for a **vanilla JavaScript project** why would you use it? Don't get me wrong, it is great as a module bundler, which makes it perfect for working with the latest frameworks, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/). For anything else it just seems like overkill, requiring so much configuration it could be days before you actually start writing any code.

Given that, I aim to present a front end build set up here using [Gulp](https://gulpjs.com/) that is framework ~~agnostic~~ _atheist_, completely portable, and easy to use for mostly all projects, requiring little to no configuration whatsoever.

 > Once you've written this `gulpfile.js`, you'll never have to touch it again...for any project.

### Overview

That previous statement seems like a bold claim - "you'll never have to touch it again" - but it's true. Right here, right now, we're going to write a `gulpfile.js` that will take it's entire configuration from a simple JSON object that will be part of the `package.json` for whatever project you are working on. If the projects you work on happen to have similar structures, you might not ever even have to change the configuration object.

### What do we need in a build?

Answering that question, I'm sure a multitude of things are flying around your head. I've distilled _what I consider_ to be the most important down into this simple list.

 - Error reporting
 - JavaScript linting, ES6+ transpilation, testing.
 - LESS/Sass linting, transpilation.
 - Asset optimisation:
    - Static file (HTML, JS, CSS) minification.
    - Image optimisation.
 - Build and test the application in CI.


I'm sure you can think of many other things, as can I. But I assure you that the framework we're about to lay out will make it simple to add it in and forget about it.


### Importing our Gulp plugins

Usually our standard gulpfile starts off with 20+ lines of `import lib from 'gulp-lib';` or `const lib = require('gulp-lib');` over and over...and over.

 > **Question:** Why do we need to do this?

 > **Answer:** We don't.

We've already declared all of our dependencies in our `package.json` under `devDependencies`, so why not just import them from there?

Luckily for us there's a handy Gulp plugin that can do just that: [gulp-load-plugins](https://www.npmjs.com/package/gulp-load-plugins). So the first thing we'll do is install that.

```javascript
npm install --save-dev gulp-load-plugins
```

And in `gulpfile.js` we load all of our `gulp-*` plugins.

```javascript
// gulpfile.js

const $ = require('gulp-load-plugins')(
    {
        pattern: ['*'],
        scope: ['devDependencies']
    }
);
```

There are plenty more options you can pass in if you [read the docs](https://www.npmjs.com/package/gulp-load-plugins#options). This will be necessary for including plugins that aren't prefixed with `gulp-`.

For our purposes this should be just about enough. We can use our plugins using the `$` like `$.liveReload.listen()`, as you'll see when we start adding our tasks.

So, on with our list!

### Error reporting

Most gulp plugins have error reporting built in, and usually you can pass in a callback function to handle that error. For consistency, that's exactly what we're going to do.

```javascript
// gulpfile.js

const handleError = error => $.fancyLog(`Error: ${error}`);
```

Whenever we need to handle an error, we can just call `handleError` and the error will be logged to the console.

### JavaScript tasks

There were a number of tasks we had in our list, so we'll go through them one-by-one.

#### Linting


#### Testing

For this, I'm going to be using [Karma](https://karma-runner.github.io/1.0/index.html) as our test runner, but you're free to use whatever you prefer.

```javascript
gulp.task('test:js', () => {
    $.fancyLog('--> Testing JavaScript');

    const server = new karma.Server({
        configFile: `${__dirname}/karma.conf.js`,
        singleRun: true
    });

    server.start();
});
```

#### Transpiling



