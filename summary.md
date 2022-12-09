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
