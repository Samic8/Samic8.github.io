---
layout: post
title:  "Using require and transclusion in Angular1 components"
date:   2017-04-11 +1000
categories: angular1
---

When creating a 'select/dropdown' component we need to pass a list to the component which it then uses to display the available options for the user to select.

The simplest way to do this is to pass a array in the component binding, which it then internally repeats and fires some event when the user chooses a option.

In these examples our select is called `dropdown`
{% highlight html %}
<dropdown options="['add','edit','delete']" on-option-select="onOptionSelect(selectedOption)"></dropdown>
{% endhighlight %}

The limitation on this approach is that we are then limited to use the same template for each use of the component.

Another approach is to use a combination require and transclusion to make our component usage look like the following, which will allow us to use custom templates for the options in each usage of our dropdown component.
{% highlight html %}
<dropdown on-option-select="onOptionSelect(optionName)">
  <dropdown-option ng-repeat="option in options" option-name="{{option}}">{{option}}</dropdown-option>
</dropdown>
{% endhighlight %}

First we want to setup the parent `dropdown` component 

{% highlight js %}
myApp.component('dropdown', {
  template: `
    <div class="dropdown">
      <div class="dropdown__button" ng-click="$ctrl.toggleDropdown()">Select a option &or;</div>
      <div class="dropdown__box" ng-show="$ctrl.showDropdown">
        <ng-transclude></ng-transclude>
      </div>
    </div>
  `,
  transclude: true,
  bindings: { onOptionSelect: '&' },
  controller: dropdownController,
});
{% endhighlight %}

The `ng-transclude` element will be replaced with multiple `dropdown-options` components which we will build next. We also want to setup functions on the dropdown controller, which will be used in the template above.

{% highlight js %}
function dropdownController() {
  const viewModel = this;
  
  viewModel.addOption = addOption;
  viewModel.toggleDropdown = toggleDropdown;
  
  function addOption(option) {
    viewModel.options.push(option);
  }
  
  function removeOption(option) {
    viewModel.options.splice(options.indexOf(option), 1);
  }
  
  function toggleDropdown() {
    viewModel.showDropdown = !viewModel.showDropdown;
  }
}
{% endhighlight %}

You will notice that there are some functions here that we are not using within our template. The `addOption` and `removeOption` functions both modify the internal options data model, these functions we will use in our child `dropdown-option` component.

Next lets setup our `dropdown-option` component. Using the require object we can specify that this component must be used within our previously defined parent component `dropdown`.

{% highlight js %}
myApp.component('dropdownOption', {
  template: `
    <ng-transclude class="dropdown__box__option" ng-click="$ctrl.onOptionClick()"></ng-transclude>
  `,
  transclude: true,
  require: { parent: '^dropdown' },
  bindings: { optionName: '@' },
  controller: dropdownOptionsController,
});
{% endhighlight %}

We now have access to the parent `dropdown` components controller within `dropdown-options` controller, using the `parent` property that we specified in our `require` object above.

The `parent.addOption` function is now used [`$onInit()`](https://blog.thoughtram.io/angularjs/2016/03/29/exploring-angular-1.5-lifecycle-hooks.html) to register the dropdown option, and the `parent.removeOption` is used [`$onDestroy()`](https://blog.thoughtram.io/angularjs/2016/03/29/exploring-angular-1.5-lifecycle-hooks.html) to remove the option from our  parent `dropdown` options data model.

{% highlight js %}
function dropdownOptionsController() {
  const viewModel = this;
  
  viewModel.$onInit = $onInit;
  viewModel.onOptionClick = onOptionClick;
  
  function $onInit() {
    viewModel.parent.addOption(viewModel.optionName);
  }
  
  function onOptionClick() {
    viewModel.parent.onOptionSelect({optionName:viewModel.optionName});
    viewModel.parent.toggleDropdown();
  }
  
  function $onDestroy() {
    viewModel.parent.removeOption(viewModel.optionName);
  }
}
{% endhighlight %}

We can also trigger the `parent.onOptionSelect()` from the `dropdown-option` component.

Note: We never ended up using the options data model on our `dropdown` component, but I thought it was useful to add to show how you can communicate between parent/child components using `require`.

## Complete Example
<iframe style="width: 100%; height: 600px" src="https://embed.plnkr.co/S2txfWYJ8L1MVi4Gezxg/" frameborder="0" allowfullscren="allowfullscren"></iframe>
