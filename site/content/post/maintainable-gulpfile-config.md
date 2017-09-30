+++
title = "Creating a maintainable gulpfile.js"
description = "Configuring Gulp across multiple projects can become tedious and tiresome. Fortunately, it's relatively easy to create a reusable and maintainable gulpfile.js"
tags = []
date = "2017-09-10T07:48:41.993Z"
categories = [
    "JavaScript",
    "Development",
    "Architecture",
    "Front End Ops"
]
+++

![Gulp banner](/images/gulp-banner.jpg)

It may be the new kid on the block, but _why_ would you use [Webpack](https://webpack.js.org/) for literally everything? Don't get me wrong, it has its use cases where it is perfect, such as working with the latest frameworks, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/). But for a **vanilla JavaScript project** it just seems like overkill.

In these cases my go to build tool of choice is [Gulp](https://gulpjs.com/) on mostly all projects which usually don't require much more than linting, transpiling, running unit tests, and deploying.

The are only two downsides to Gulp - or at least its configuration - as I see it. Both of them pertaining to maintainability. But hopefully, we'll be able to address those today.

### What are Gulp's configuration downsides?

The number of plugins we actually need to get Gulp working 'just the way we want it' can be incredibly large. This means that the first lines of code in any `gulpfile.js` usually consist of `import`ing or `require`ing (depending on your flavour) any number of plugins.

This list of plugins is usually a mirror of much of the contents of the `devDependencies` in our `package.json` file. Remove a plugin from one, we need to remember to remove it from the other.

Often our `gulpfile.js` is written in tandem with the rest of our project. This means lots of hardcoded globs, paths, and filenames littering it. Again any changes to one, requires a change to the other.

Our `package.json` should be concerned with the meta data of our project, such as its structure and what dependencies it may have.

Our `gulpfile.js` should be concerned with building our project.

 > Duplicating all of that information again goes against the _**DRY**_ (Don't Repeat Yourself) principle and the principle of _**separation of concerns**._

Once we've defined a pattern to solve these issues, configuring your Gulp build tasks will be so easy, you'll wonder what you've been doing for all these years ...at least I did.

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

Here we're assigning them to `$`, but we could just have easily assigned them to any other valid variable name.

We can use our plugins using `$.`, e.g. `$.liveReload.listen()`, as you'll see when we start adding our tasks.


### Importing our file paths

Rather than hard-coding in our file paths and globbing patterns for each task, we'll maintain an object in our `package.json` specific to our project. That is what our `package.json` is for after all.

To access these we'll have to import it.

```javascript
// gulpfile.js

const pkg = require('./package.json');
```

This has now imported our entire `package.json` as one big object, which we can pull properties out of as we wish.

Not just the file paths and globbing patterns mentioned earlier, but any other meta data of our project, such as its name, author, or license.

```javascript
// gulpfile.js

const name = pkg.name;  // "gulp-slack-upload"
const author = pkg.author.name;  // "Jacob Ward"
const license = pkg.licenses.type;  // "MIT"
```


### Configuring a Gulp task

All of this is well and good at an intellectual level, but how do we actually do something practical with it? Let's choose a common build task: transpiling CSS from Sass.

I'm sure you can think of many other things. But I assure you that the framework we're about to lay out will make it simple to add whatever tasks you like.

First we're going to need to install all of our plugins.

```javascript
npm install --save-dev gulp gulp-sass gulp-cssnano gulp-autoprefixer gulp-sourcemaps gulp-plumber gulp-cached gulp-size gulp-concat fancy-log
```

That's already 9 plugins just to transpile our Sass. More lines would have been taken up declaring those in our `gulpfile.js` than have been with our new configuration.

We also need to define where our paths are.

```json
// package.json
{
    "name": "easy-gulp-config",
    "version": "0.0.1",

    "paths": {
        "src": {
            "sass": "./src/sass/**/*.{scss,css}"
        },
        "dist": {
            "css": "./dist/assets/css/"
        },
        "names": {
            "css": "main.min.css"
        }
    }
}
```

With all of that in place, let's go ahead and configure our task.

```javascript
const gulp = require('gulp');
const pkg = require('./package.json');

const $ = require('gulp-load-plugins')(
  {
    pattern: ['*'],
    scope: ['devDependencies']
  }
);

const errored = error => $.fancyLog.error(error);

gulp.task('build:sass', () => {
  return gulp.src(pkg.paths.src.sass)
    .pipe($.plumber({errorHandler: errored}))
    .pipe($.sourcemaps.init({loadMaps: true}))
    .pipe($.sass().on('error', $.sass.logError))
    .pipe($.cached('sass_cache'))
    .pipe($.autoprefixer())
    .pipe($.sourcemaps.write('.'))
    .pipe($.concat(pkg.paths.names.css))
    .pipe($.cssnano(
      {
        discardComments: {removeAll: true},
        discardDuplicates: true,
        minifySelectors: true
      }
    ))
    .pipe($.size(
      {
        gzip: true,
        showFiles: true
      }
    ))
    .pipe(gulp.dest(pkg.paths.dist.css));
});
```

### Configuring additional tasks

To configure more tasks we just have to go through the same process again.

 1. Install any required gulp plugins as `devDependencies`
 1. Configure paths and variables in `package.json`
 1. Create a new `gulp.task` using:
   - The `$.` prefix for plugin names
   - The `pkg.paths` object notation for paths and variables.

After a while, you will have built up a `gulpfile.js` so reusable and maintainable that you won't need to touch it, except for adding new tasks.

All of the configuration will be stored in each project's `package.json` file as meta data, just as it should be.
