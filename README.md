# $digest cycle and dirty-checking

## Overview

We've talked about `$scope.$apply()`, but we don't actually know what it does yet. Let's take a look.

## Objectives

- Describe $scope.$apply()
- Describe $scope.$digest()
- Use $scope.$apply() inside a native DOM API

## $digest cycle

So far, we've been updating our $scope object and our controller and the changes get reflected in the view. This is awesome, but how is it actually done?

In comes the digest cycle! The digest cycle is a cycle that checks all of our values and fires callbacks if there are changes.

When we put an expression in the view (such as `{{ ctrl.name }}`), Angular creates a "watcher". This watcher then goes into an array in `$scope.$$watchers`. This watcher has a function that returns the latest value of `ctrl.name`, and a callback to update the view with the latest `ctrl.name` value.

When we run the digest cycle, we loop through all of our watchers and run the functions. The function runs, returns `ctrl.name` and checks it against its last known value. If the value is different, the callback fires, updating the view with the latest value of `ctrl.name`.

These watchers are scoped to our `$scope` object, meaning that if we're updating values in our scope, we don't have to update **all** the scopes that exist in our Angular application. For instance, if we're just updating values inside a directive, we only need to run the digest cycle inside that directive - no need to go outside of it.

There are two ways to trigger the $digest cycle - `$scope.$digest()` and `$scope.$apply()`.

### $digest()

`$scope.$digest()` runs the digest cycle on the given `$scope` object, and goes down all the children scopes. This is as far as the cycle goes - it doesn't go upwards, only downwards. If we went upwards, we'd go up scopes until we reached the root scope.

### $apply()

`$apply()` is the same as `$digest()` - except from the fact it runs `$digest` from our `$rootScope`. This means whilst `$apply()` does only go downwards, much like `$digest()`, it goes downwards from the `$rootScope`, meaning **all** of our watchers are checked and updated. This means any nested controllers will have their digest cycle ran.

## Manual digest

The reason why we have to call `$scope.$apply()` when we update values in our event listener is because Angular doesn't know that we've updated the scope. How does it know when we use `ng-click`, etc? Angular actually runs `$scope.$apply()` itself after the click event that `ng-click` provides us. This means that we're basically doing the same as Angular does when we run `$scope.$apply()` in our events.