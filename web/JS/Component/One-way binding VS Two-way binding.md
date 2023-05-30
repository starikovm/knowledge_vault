## Angular

When angular sets up databinding two "watchers" exist (this is a simplification)

```javascript
//js
$scope.name = 'test';
$timeout(function()  { $scope.name = 'another' }, 1000);
$timeout(function()  { console.log($scope.name); }, 5000);

<!-- html --->
<input ng-model="name" />
```

The input will start out with `test`, then update to `another` in 1000ms. Any changes to `$scope.name`, either from controller code or by changing the input, will be reflected in the console log 4000ms later. Changes to the `<input />` are reflected in the `$scope.name` property automatically, since `ng-model` sets up watches the input and notifies `$scope` of the changes. Changes from the code and changes from the HTML are **two-way binding**. (Check out [this fiddle](http://jsfiddle.net/HB7LU/21693/))

# React

React doesn't have a mechanism to allow the HTML to change the component. The HTML can only raise events that the component responds to. The typical example is by using `onChange`.

```javascript
//js
render() { 
    return <input value={this.state.value} onChange={this.handleChange} />
}
handleChange(e) {
    this.setState({value: e.target.value});
}
```

The value of the `<input />` is controlled _entirely_ by the `render` function. The only way to update this value is from the component itself, which is done by attaching an `onChange` event to the `<input />` which sets `this.state.value` to with the React component method `setState`. The `<input />` does not have direct access to the components state, and so it cannot make changes. This is **one-way binding**. (Check out this [codepen](http://codepen.io/tyrsius/pen/OMbOqP?editors=001))

[](https://stackoverflow.com/a/34520204/7630425 "Short permalink to this answer")