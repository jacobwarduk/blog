+++
title = "Wait for content to be updated and rendered in AngularJS 1.x"
description = "Listening for the $viewContentLoaded or $onChanges events don't actually solve the problem. So here's a solution that does using our own custom directive."
tags = []
date = "2017-10-21T12:15:14.178Z"
categories = [
    "JavaScript",
    "Development",
    "AngularJS"
]
+++

![AngularJS logo with binding braces](/images/update-angular-js-bindings.png)

I've run into this problem before on another project, and the other day I ran into it again - waiting for data on scope to be updated **and** rendered on the page before doing something.

## Scenario 1 - MVVC Architecture

The application was being developed using an off-the-shelf theme implementing [UI Bootstrap](https://angular-ui.github.io/bootstrap/) and [Jasny Bootstrap](http://www.jasny.net/bootstrap/), among others.

Ultimately, the issue was that the width of the `navbar-header` and the `profile-dropdown` elements were being calculated using JavaScript in an AngularJS directive _after_ the DOM was loaded and whenever there was a page resize in the `link` function, something like this.

```javascript
// directive

link: function() {
  angular.element(document).ready(function() {
    $timeout(function() {
      resetWidth();
    }, TIMEOUT_GLOBAL_RENDERING);
  });

  angular.element($window).bind('resize', function() {
    resetWidth();
  });
}
```

A hack if ever I'd seen one. In fact, it simply did not work.

See `angular.element(document).ready()` will execute as soon as the DOM is ready, setting the appropriate widths for the elements _before_ the promises which fetched the data for them had been resolved. When said promises did resolve and populate the elements with content, that caused them to expand and overflow the width of the page.

You might say this was an easy problem to solve, but with the complexity of so many different libraries intertwined and being maintained outside of our application, we didn't have the option of rewriting the existing functionality. We had to extend it.

The initial thought was to use [`$viewContentLoaded`](https://docs.angularjs.org/api/ngRoute/directive/ngView#event-$viewContentLoaded).

The issue with this approach is that `$viewContentLoaded` applies to the HTML, not to the content in its bindings. So, this would fire and redraw the page _before_ the promises resolved and _before_ the data in the bindings changes in the view.


## Scenario 2 - Component-based Architecture

This application is being developed using an in-house developed theme using [AngularStrap](http://mgcrea.github.io/angular-strap/).

The pattern for page loads and refreshes when I joined the project was already well established, and used throughout the application.

```javascript
// controller

vm.loading = true;

promise()
  .then(
    function(success) {
      // Do something on success
    },
    function(error) {
      // Do something on failure
    }
  )
  .finally(
    function() {
      vm.loading = false;
    }
  );
```

It worked for the application at that point, but as my team and I began further development, this implementation did not scale well.

While the data had been retrieved, `vm.loading` was being set to `false` a long time before the data had chance to work its way down through the component tree and be propagated to the view.

This caused the loading spinner in the view to disappear while the old data was still visible, with a delay before the fresh data replaced it.

We investigated a number of solutions, including the obvious component lifecycle hooks [`$postLink`](https://toddmotto.com/angular-1-5-lifecycle-hooks#using-postlink) and [`$onChanges`](https://toddmotto.com/angular-1-5-lifecycle-hooks#onchanges).

Again, we had the same issue that both `$postLink` and `$onChanges` are fired _before_ the data in the bindings actually changes in the view.


## The Solution - A Custom Directive

The problem is obvious - we need to wait until the data in the view has been updated _before_ we apply the successive logic.

The solution was equally obvious - essentially create our own 'hook' which could be applied to elements as a directive that would be fired _after_ the data in the view has been updated.

 - Step 1 - Add our process to the end of the [message queue](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Queue).
 - Step 2 - Wait for the element containing the data to be '[ready](https://docs.angularjs.org/api/ng/function/angular.element)'.
 - Step 3 - [Apply](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$apply) any scope (data) changes to the element.
 - Step 4 - [Broadcast](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$broadcast) that this process has completed.
 - Step 5 - [Listen](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$on) for this event and act upon it.

The result of this is the following directive and implementation.

```javascript
// directive

(angular => {
  'use strict';

  const elementReady = ($timeout, $rootScope) => {
    return {
      restrict: 'A',
      link(scope, element, attrs) {
        $timeout(() => {
          element.ready(() => {
            scope.$apply(() => {
              $rootScope.$broadcast(`${attrs.elementReady}:ready`);
            });
          });
        });
      }
    };
  };

  elementReady.$inject = ['$timeout', '$rootScope'];

  angular
    .module('ElementReady', [])
    .directive('elementReady', elementReady);
})(angular);
```

```html
<!-- template -->

<div element-ready="search-results">
    {{ vm.data }}
</div>
```

```javascript
// controller

$scope.$on('search-results:ready', () => {
    // Take action after the view has been populated with the updated data
});
```

If you've faced this problem and solved it in a different way [I'd love to hear about it](https://github.com/jacobwarduk/jacobward.io/issues/new).

Hopefully this helps somebody out there. As always, the code is [available to download on Github](https://github.com/jacobwarduk/ng-element-ready).
