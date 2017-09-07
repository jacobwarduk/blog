+++
title = "Testing ES6+ using Karma, and Jasmine"
description = "It sounds easy, but with conflicting documentation and tutorials flooding the net, I found it difficult to get this test set up correct."
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

It wasn't until I started working with [AngularJS 1.x](https://angularjs.org/) a few years ago that I actually began practicing TDD (Test Driven Development). The set up of [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/) was just so easy, it became the default combo for TDD on all of my projects.

Then I started using ES6+. Karma and Jasmine didn't seem to like this one bit. I trawled the net looking for how to get these working together, after all there was no way I was going to change my tooling just to accommodate using what was now **standardised JavaScript**. But, to my thorough disappointment, every tutorial, all documentation, came up short.

I ended up using various contrived hacks, very much in the spirit of a [Rube Goldberg machine](https://en.wikipedia.org/wiki/Rube_Goldberg_machine), passing one thing to another and back around again until finally I had something working.

This was hardly a reliable set up. Definitely not something I was confident in. And if I'm not confident in my testing set up, how can I be confident in my tests?

### How I built confidence in my test set up

I looked at what I had, what I had been using, and started to boil it down. Refactor it, if you will. To distill it into a simple, well engineered solution.

If I'm not using a modern framework (in which case I'm probably using [Webpack](https://webpack.js.org/)), I tend to use [Gulp](https://gulpjs.com/) to build my projects. It's easy, familiar, and doesn't require too much configuration to get the job done.

Here is what I finally came up with.

#### `karma.conf.js`



#### `gulpfile.js`







