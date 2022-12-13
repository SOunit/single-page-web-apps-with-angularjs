# week 1 : intro to AngularJS

- why AngularJS?

  - to handle complexity
    - coherence
      - focused module
    - reusability
  - MVVM / Model View ViewModel architecture
    - controller is ViewModel
    - $scope is for expose variable from ViewModel / Controler to View

- DI / Dependency Injection, Interpolation
  - protect `DI` from `minification`
    - `$inject(["a", "b"])`
- Interpolation
  - add `{{ exp }}` to anywhere
  - use `ng-src` to avoid error
    - `test-{{random}}.png` will be error in first time

# week 2 : filters, digest cycle, controller inheritance, and custom services

## filter, digest cycles, and data binding

- filters
  - basic sample
    - `{{var | uppercase }}`
    - `$filter("uppercase")(var)`
  - pass argument
    - `{{var | uppercase : arg1 : arg2 }}`
    - `$filter("uppercase")(var, arg1, arg2)`
- custom filters
  - create filterFactory
  - add filter to module
    - `module("app", []).filter("custom", CustomFilterFactory)`
  - add to dependency
    ```
    Ctrl.$inject = ["$scope", "customFilter"];
    function Ctrl($scope, customFilter){}
    ```
- digest cycle

  - how AngularJS work?
  - `ng-click`, `ng-keyup` leads to angular context
  - angular context has watcher to props
    - set watcher
      - `{{ var }}`
      - `ng-model`
  - manual setup of wathers

    ```
      $scope.$watch("counter", function(newValue, oldValue){
       console.log(oldValue);
       console.log(newValue);
    })
    ```

  - check number of watchers

    ```
    console.log($scope.$$watchersCount);
    ```

  - check digest loop execution

        ```
            $scope.$watch(function(){
            console.log("Digest Loop Fired!");
            })
        ```

  - $digest & $apply

    - not working sample
      ```
      setTimeout(function(){
          $scope.count++;
      }, 2000)
      ```
    - digest sample
      ```
      setTimeout(function(){
          $scope.count++;
          $scope.$digest();
      }, 2000)
      ```
    - apply sample

      ```
      setTimeout(function(){
          $scope.$apply(function(){
              $scope.count++;
          });
      }, 2000)
      ```

    - use angular custom function
      ```
      $timeout(function(){
        $scope.count++;
      }, 2000)
      ```

  - 2-way, 1-way, 1-time binding

## Looping, Controller As Syntax

### Point / Opinion / Summary

- what is the benefit of scope inheritance and controller as syntax?

  - small focused controllers are better than one big controller
  - how to handle multiple controllers
  - how to access data in controller
  - no use for `$scope`
  - `this` is pointing to controller instance
    - AngularJS use controller function as constructor function

### lecture memo

- scope inheritance
  - nest controller
    - child controller inherit parent controller
    - child controller can access to parent controller
      - `$parent`
- controller as syntax
  - use `this` pointing to controller instance instead of `$scope` to expose variable to `View`

## creating and configuring custom services

### point

- multiple controllers is good practice
- custom service is for
  - share data and code
- good sample
  - they share data and logic
  - constructor functions
    - ShoppingListAddController
    - ShoppingListShowController
    - ShoppingListService

### lesson

- controllers responsibility

  - do
    - setup initial value for `$scope`
    - add behaviour to `$scope`
      - means binding event and update `$scope` value to update View
  - NOT do
    - handle business logic
    - shared data or code

- sample

  ```
  angular.module("app", [])
  .controller("ctrl", Ctrl)
  .service("CustomService", CustomService);
  ```

  - "CustomService" is the label used in controller DI argument
  - CustomService function is used as constructor function

- singleton pattern

  - service is singleton
    - easy to share data across `controllers`

- lazy instantiated

  - instantiated when it is added to dependency

- factory

  - factory pattern
    - cental place to produces new objects or functions
    - can produce anything
    - can be used to produce dynamically customizable services

- service vs. factory
  - service
    - singleton
  - factory
    - not singleton
    - can pass argument

# week3

## asynchronous behavior, promises, ajax in AngularJS

## intro to custom directive

- directive summary

  - directive is an HTML marker AngularJS compile to html
  - registered factory function must return DDO
    - DDO is directive definition object
    - factory function get invoked only once

- syntax

  - template
  - templateUrl
  - restrict

- normalization

  - directive("listItemDescription", ListItemDescription)
    - `listItemDescription` should be lowercase start
      - translated to list-item-description
    - `ListItemDescription` should be uppercase
      - it's constructor function
  - <list-item-description>

- restrict

  - AE is default
    - A - attribute
      - only expand function
      - no content
      - example
        - `ng-repeat`
    - E - element
      - when has content or template
      - example
        - `list-item` tag

- isolate scope

## point

- directive can have controller
- use isolation scope to avoid dependency to parent controller

  - scope is to get data from outside of directive
  - scope sample

    - directive

      ```
      scope: {
        myProp: '=attributeName'
      }
      ```

    - html

      ```
      <directive attribute-name="some data">
      ```

    - template
      ```
      value from outside: {{myProp}}
      ```

- use controller in directive to have independent directive
  - controller as syntax
  - scope and binding
    - `=`
    - `@`
    - `<`

## lesson

- to make directive isolate from controller scope
- can use anywhere, no dependency on controller
- bidirectional
  - `=`
  - sample
    ```
    scope{
      myProp: '=attributeName'
    }
    ```
    ```
    scope{
      myProp: '='
    }
    ```
  ```
  - `=?`
    - attribute is optional
  - `@`
    - unidirectional
    - value is string
  - `<`
    - one-way binding
    - better than two-way binding when 2-way is not necessary
  ```

## lesson3 directive APIs, manipulating DOM, transclude

- to execute parent controller method in child directive

  - use `&`
  - sample
    - list data is in parent controller
    - directive is responsible for display and delete
      - child directive have to execute parent delete method

- DOM manipulation and jQuery

  - angular support jQuery wrapper
    - limited than jQuery
  - can also use jQuery

- transclusion
  - sample
    - dialog box
    - same behavior
    - different content
  - steps
    - set `transclude: true` in directive definition
    - wrap with directive
    - set `ng-transclude` to import content

# lesson4: components, events, modules and routing

## components and component-based architecture

- component

  - special type of directive with simpler configuration than directives
  - simpler syntax, wrapping directive
  - Angular v2 uses component exclusively
  - component is good practice

- best practice of component architecture

  - component control their own data and view
    - use isolation scope
  - component has well defined API - inputs and outputs
    - use `<` and `@`
    - never change property of passed in object or array
    - output
      - use `&` for component event callback
    - pass data to callback through param map `{key: value}`
  - components have well-defined lifecycle
    - $onInit
    - $onChange(changeObj)
      - changeObj.currentValue
      - changeObj.previousValue
    - $postLink
      - similar to `link` in directive
    - $onDestroy
      - when release from memory, like refresh etc.
  - application is a tree of components
    - minimum 2-way data binding

- $doCheck
  - invoked by all digest loop
  - lifecycle method to update
  - to avoid using $scope.$watch

## angularJS event system and modules

- communication between components is difficult

  - like redux in react
  - singleton service is available but not good approach
    - all logic is in service? not good
  - use event system to handle this

- public subscribe design pattern

  - publisher
    - create event
  - subscriber
    - listen to event

- $scope.$emit

  - up scope chain

- $scope.$broadcast

  - down the scope chain

- $scope.$on

  - listen event

- module
  - separate files
  - first create module, then components etc.
  - file name
    - test.component.js
    - test.module.js
    - etc.

## introduction to single-page routing with ui-router

## handling data with routing

## nested views and transition events

# lesson5: form validation, testing, restaurant site development

## form validation and introduction to testing javascript

## testing AngularJS controllers and services

## testing AngularJS directives and components

## visiting with client, old site overview, restaurant server setup

## coding the restaurant site in AngularJS
