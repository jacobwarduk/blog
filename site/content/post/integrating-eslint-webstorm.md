+++
title = "Integrating ESLint settings with WebStorm"
description = "Setting up the perfect development environment takes patience and a lot of fine tuning. Getting linting running correctly inside your editor is imperative."
tags = []
date = "2017-08-26T05:08:48.817Z"
categories = [
    "JavaScript",
    "Development",
    "Architecture",
    "Front End Ops"
]
+++

I recently joined a distributed team, where upon joining I was told the _coding standards_ the team were '**_loosely_**' following. It is a lengthy read, but fortunately I was already familiar with it, having used it myself on a previous project.

The term '**_loosely_**' was strongly emphasised by the developer helping me with the onboarding process. When I finally got to look around to familiarise myself with the codebase, I understood why.

### What was the problem?

You could tell just by looking, which code was written by the various different developers. It may be laziness, apathy, or simply people wanting to write code how they want to write it (that's not how a team works, btw).

However you look at it, this is a problem. so, as part of the micro re-architecting we had planned I **added ESLint into the build chain** to enforce all of the rules in our style guide.

### Why adding ESLint doesn't solve the problem

Simply adding [ESLint](https://eslint.org) into the build chain, spitting out warnings and errors everytime a build is run is great and everything. But once the errors are fixed, the warnings can just be ignored (and often are).

 > You should never fail a build due to stylistic differences. Only syntactic errors that will prevent the code from running correctly should fail a build.

#### A couple of examples

In these examples, the code is perfectly valid, and will build correctly. Not breaking anything. However, the style is completely different.

---

Using `React.createClass` vs. using ES6 class `extends React.Component`.

```javascript
const Main = React.createClass({
    render() {
        return (
            <div>Hello world</div>
        );
    }
});
```

```javascript
class Main extends React.Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            <div>Hello world</div>
        );
    }
}
```

---

Using AngularJS array dependency injection vs. using `$inject` property annotation.

```javascript
app.controller('MainCtrl',
    ['$scope', 'MainService',
        function($scope, MainService) {
            $scope.message = MainService.getMessage();
        }
    ]
);
```

```javascript
app.controller('MainCtrl', MainCtrl);

MainCtrl.$inject = ['$scope', 'MainService'];

function MainCtrl($scope, MainService) {
    $scope.message = MainService.getMessage();
}
```

---

One could reject either of these during code review, but neither should break the actual build.

So, what can we do to ensure that everybody is singing from the same hymn sheet?

### Integrate ESLint with WebStorm using your config

I'm going to assume at this point that everybody working on a project has at least the basic shared config. Either by using a common [`.editorconfig`](http://editorconfig.org/) file committed to the repository or by importing a shared `settings.xml` file which matches your style guide.

Initially setting this up manually can be a long and arduous task, going through each rule in your style guide and ensuring it is reflected correctly in your config, but it is worth it in the long run.

If you're not already using [WebStorm](https://www.jetbrains.com/webstorm/), I would suggest you check it out. It's a great IDE for Front End and JavaScript development. The company behind it, [JetBrains](https://www.jetbrains.com), also make IDEs for other languages including Python (PyCharm), Java (IDEA), PHP (PHPStorm), C/C++ (CLion), among others. They all have similar interfaces, so jumping between projects using different tech stacks is really easy.

Anyways, let's get back on track. So we have WebStorm installed and are using a common basic configuration. What do we do now?

It's time to put ESLint to work, and fortunately for us WebStorm actually comes with ESLint embedded as one of its 'Code Quality Tools'.

You know what? This is one of the most simple things to do that a lot of people overlook.

Open up the `Preferences` window, then in the left navbar navigate to `Languages & Frameworks` ➡ `JavaScript` ➡ `Code Quality Tools` ➡ `ESLint`.

Once there, check the `Enable` checkbox and all the for fields should become enabled. From here, it's easy...

1. **Node interpreter:** this is the path to your installation of Node.js, e.g. `/usr/local/bin/node` or `C:\Users\JWard\AppData\Roaming\npm`, depending on what operating system you are using.
1. **ESLint package:** this is the path to the ESLint package you wish to use, this can be local to your project, e.g. `/Users/jacobward/Dev/myproject/node_modules/eslint` or (as I would recommend) your global installation `/usr/local/lib/node_modules/eslint`.
1. **Configuration file:** rather than using the 'Automatic search', which can be flaky, we'll be specifying our own configuration file. This should be the `.eslintrc.*` file that everybody should already be using for linting in the build.

Click `Apply` or `Save` and voila, we now have linting inside our editor which will pop up errors and warnings as we make them.

![ESLint highlight error in code](/images/eslint-error-01.png)

![ESLint highlight error in file name](/images/eslint-error-02.png)

No more excuses for not following the style guide. Your files will be marked as being in error.
