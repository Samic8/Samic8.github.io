---
layout: post
title:  "Angular Icon System"
date:   2017-03-21 +1000
categories: angular icons angular1
---

This is a example of the angular icon system that I use, it's aim is to keep icon naming consistent and the html needed to generate a icon small.

<div style="text-align: center">	
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#cash-dollar"></use>
    </svg>
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#bubbles"></use>
    </svg>
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#calculator"></use>
    </svg>
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#apartment"></use>
    </svg>
</div>

## Displaying a icon using SVG symbols
A prerequisite for the icon system is that every icon has a unique name. The icon system relies on a single SVG with multiple symbols, we then [`<use>`](https://developer.mozilla.org/en/docs/Web/SVG/Element/use) the symbol in one or more places.

This SVG is prepended to the body of our html. We are using a Internally referenced SVG in this example another  approach that would work with this icon system is to use [externally referenced svg files](https://css-tricks.com/svg-use-with-external-reference-take-2/).
{% highlight html %}
<svg style="position: absolute; width: 0; height: 0; overflow: hidden">
    <defs>
        <symbol viewBox="0 0 20 20" id="apartment">
            <title>apartment</title>
            <path d="..."></path>
            <path d="..."></path>
        </symbol>
        ...
    </defs>
</svg>
{% endhighlight %}

I am going to skip over how we got to this point where the SVG's are already in the document body. It would be tedious to manage our svg symbols by having them statically in the body of our html.

 With this icon system in its real world usage I concatenate the SVG's with a 'icon build system' which I will explore in another post, the build system allows me to place all of my icons as single SVG's into a directory, the build system then collects all of those icons and injects them into the body.

Once we have the SVG symbols injected into our html we can [`<use>`](https://developer.mozilla.org/en/docs/Web/SVG/Element/use) our SVG's.
{% highlight html %}
<svg class="icon">
    <use xlink:href="#apartment"></use>
</svg>
{% endhighlight %}
(explain xlink href)

We are referencing the 'apartment' SVG that is in our SVG symbols. The above example produces our 'apartment' icon  
<div style="text-align: center;">
    <svg style="width: 40px; height: 40px; display: inline-block;"><use xlink:href="#apartment"></use></svg>
</div>

## Building a icon component
Now that we have got the basic version of this icon system working we can begin to build on top with angular. We are going to create a angular [component](https://docs.angularjs.org/guide/component) which uses a variation of the above html as its template. Instead of the hard coded '#apartment' we use the injected string `iconName` from the our `svgIcon` component.
{% highlight javascript %}
.component('svgIcon', {
    template: '<svg class="icon">
                  <use xlink:href="{% raw %}{{$ctrl.iconName}}{% endraw %}"></use>
              </svg>',
});
{% endhighlight %}

Lets define `iconName` as a binding, `iconName` references the symbol `ID` directly from our svg defs.
{% highlight javascript %}
.component('svgIcon', {
    template: '<svg class="icon">
                  <use xlink:href="{% raw %}{{$ctrl.iconName}}{% endraw %}"></use>
              </svg>',
    bindings: {
        iconName: '<',
    },
    controller: function (){}
});
{% endhighlight %}

To produce our 'apartment' icon we then only need the following html. Assuming we have our [`angular modules`](https://docs.angularjs.org/guide/module) wired up correctly.
{% highlight html %}
<svg-icon icon-name="'apartment'"><svg-icon>
{% endhighlight %}

<div style="text-align: center;">
    <svg class="icon" style="width: 40px; height: 40px; display: inline-block;"><use xlink:href="#apartment"></use></svg>
</div>

Here is a working example of all of the concepts we have covered so far. There are some extras in the example that I have left out so far for simplicity. Assumed knowledge here is the [`$onChanges` life cycle hook](https://blog.thoughtram.io/angularjs/2016/03/29/exploring-angular-1.5-lifecycle-hooks.html#onchanges) and [`ng-style`](https://docs.angularjs.org/api/ng/directive/ngStyle) used to set our icon size.

<p data-height="400" data-theme-id="0" data-slug-hash="xqXzqa" data-default-tab="html,result" data-user="Samic8" data-embed-version="2" data-pen-title="Angular Icon System (Icon)" class="codepen">See the Pen <a href="https://codepen.io/Samic8/pen/xqXzqa/">Angular Icon System (Icon)</a> by Sam Dawson (<a href="http://codepen.io/Samic8">@Samic8</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Aliases

Aliases are the most powerful part of this icon system, it helps keep icons consistent across our application. Aliases provide a way for an icons unique name to be independent of their usage name, allowing the same icon to be used for multiple use cases with different naming. 

Another benefit is being able to replace icons with without having to do a find and replace. Now lets get into how we go about getting aliases working for our icon system.

We want to add a extra binding to our `svgIcon` [component](https://docs.angularjs.org/guide/component) called `aliasName`. Here we will use one-way binding (<), which allows us to watch for changes and update icons on the fly.
{% highlight javascript %}
.component('svgIcon', {
    template: '<svg class="icon">
                  <use xlink:href="{% raw %}{{$ctrl.iconName}}{% endraw %}"></use>
              </svg>',
    bindings: {
        iconName: '<',
        iconAlias: '<',
    },
    controller: function (){}
});
{% endhighlight %}

For our apartment icon, lets set up a alias name of 'building'. So that we can use the [component](https://docs.angularjs.org/guide/component) like so
{% highlight html %}
<svg-icon icon-alias="'building'"><svg-icon>
{% endhighlight %}

We need to write some extra javascript in our controller to get this working, so far our controller has been empty. First we want to create a `aliases` object, the holds our alias/icon name associations.

{% highlight javascript %}
function svgIconController() {
	const ctrl = this;
	
	const aliases = {
		building: 'apartment',
		...
	};
}
{% endhighlight %}

Now that we have a string that does not directly associate with a symbol in our svg, we want to make sure that we translate `aliasName` input to a string that is actually associated with a symbol.

Lets change the template so it is not directly using our 'iconName' binding
{% highlight javascript %}
.component('svgIcon', {
    template: '<svg class="icon">
                  <use xlink:href="{% raw %}{{$ctrl.svgSymbolId}}{% endraw %}"></use>
              </svg>',
    bindings: {...},
    controller: ...
});
{% endhighlight %}

Now we want to get the symbol id from the associated 'aliasName' `aliases[aliasName]` and set the result to the `svgSymbolId`. Our [component](https://docs.angularjs.org/guide/component) is now multipurpose as it accepts both 'iconName' and 'aliasName', we are assuming the developer will use either/or and not both (our [component](https://docs.angularjs.org/guide/component) could be extended to error etc.)
{% highlight javascript %}
function svgIconController() {
	const ctrl = this;
	
	const aliases = {...};
	
	this.$onChanges = $onChanges;
	
	function $onChanges({iconName, aliasName, iconSize}) {
		ctrl.svgSymbolId = `#${iconName.currentValue ? iconName.currentValue : aliases[aliasName.currentValue]}`;
	}
	
}
{% endhighlight %}
<sub>[Destructuring assignment](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) is used as our function arguments</sub>

Usage of ```<svg-icon icon-alias="'building'"><svg-icon>``` will produce 
<div style="text-align:center">
    <svg class="icon" style="width: 40px; height: 40px; display: inline-block;"><use xlink:href="#apartment"></use></svg>
</div>

<p data-height="400" data-theme-id="0" data-slug-hash="VpMdzO" data-default-tab="html,result" data-user="Samic8" data-embed-version="2" data-pen-title="Angular Icon System (Aliases)" class="codepen">See the Pen <a href="https://codepen.io/Samic8/pen/VpMdzO/">Angular Icon System (Aliases)</a> by Sam Dawson (<a href="http://codepen.io/Samic8">@Samic8</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Bonus 

### ng-repeat
With our icon system wired up to accept one way bindings we can use `ng-repeat` to produce dynamic lists of icons. In our root controller we can set up a array of iconName (we could also use iconAlias)

{% highlight javascript %}
function rootCtrl($scope) {
    $scope.iconsToRepeat = [
        'cash-dollar',
        'bubbles',
        'calculator',
        'apartment'
    ];
}
{% endhighlight %}

{% highlight html %}
<div ng-repeat="iconName in iconsToRepeat">
    <svg-icon icon-name="iconName"></svg-icon>
</div>
{% endhighlight %}

Which produces our icons
<div style="text-align: center">	
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#cash-dollar"></use>
    </svg>
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#bubbles"></use>
    </svg>
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#calculator"></use>
    </svg>
    <svg style="font-size: 40px; width: 1em; height: 1em;">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#apartment"></use>
    </svg>
</div>

### Colors
This icon system can be used with the [color system]({% post_url 2017-03-11-front-end-color-organisation %}) outlined in my previous post.

We can use [`currentColor`](https://developer.mozilla.org/en/docs/Web/CSS/color_value#currentcolor_keyword) as the fill property on the svg inside our `svgIcon` [component](https://docs.angularjs.org/guide/component) and then on the components element use a color class, the svg will inherit our color.

{% highlight css %}
.icon {
    fill: currentColor;
}
{% endhighlight %}
<sub>Note: That the icons we are using in these examples are fill only icons. This sort of color inheritance will prove difficult if we needed to use icons with multiple colors, this is a limitation.<sub>

### Icon sizing
In this icon system we are using icons that have equal width and height. Therefore being able to simultaneously change the width and height with a single value, we can set our icons width and height properties to `1em`. The icons will now equal the current font-size, meaning if we set a font-size of 20px on the parent element or directly the svg will use the current font size as both its width and height.

{% highlight css %}
.icon {
    ...
    width: 1em;
    height: 1em;
}
{% endhighlight %}

To use this with our icon system we could use inline styling
{% highlight html %}
<svg-icon icon-name="apartment" style="font-size: 20px"><svg-icon>
{% endhighlight %}

If we want to do this a little cleaner we could add a `iconSize` binding to our [component](https://docs.angularjs.org/guide/component) and then apply the font-size in a inline style within the component
{% highlight html %}
<svg-icon icon-name="apartment" icon-size="20"><svg-icon>
{% endhighlight %}

## Complete example
<p data-height="400" data-theme-id="0" data-slug-hash="MpvGjy" data-default-tab="html,result" data-user="Samic8" data-embed-version="2" data-pen-title="Angular Icon System (Complete)" class="codepen">See the Pen <a href="https://codepen.io/Samic8/pen/MpvGjy/">Angular Icon System (Repeat)</a> by Sam Dawson (<a href="http://codepen.io/Samic8">@Samic8</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

<svg style="position: absolute; width: 0; height: 0; overflow: hidden">
    <defs>
        <symbol viewBox="0 0 20 20" id="apartment">
            <title>apartment</title>
            <path d="M14 6h1v1h-1V6zM14 8h1v1h-1V8zM14 10h1v1h-1v-1zM14 12h1v1h-1v-1zM14 16h1v1h-1v-1zM14 14h1v1h-1v-1zM6 6h1v1H6V6zM6 8h1v1H6V8zM6 10h1v1H6v-1zM6 12h1v1H6v-1zM6 16h1v1H6v-1zM6 14h1v1H6v-1zM4 6h1v1H4V6zM4 8h1v1H4V8zM4 10h1v1H4v-1zM4 12h1v1H4v-1zM4 16h1v1H4v-1zM4 14h1v1H4v-1zM8 6h1v1H8V6zM8 8h1v1H8V8zM8 10h1v1H8v-1zM8 12h1v1H8v-1zM8 16h1v1H8v-1zM8 14h1v1H8v-1z"></path>
            <path d="M18.5 19H18V5.5c0-.763-.567-1.549-1.291-1.791L12 2.139V.499a.5.5 0 0 0-.644-.479L2.314 2.733C1.577 2.954 1 3.73 1 4.499v14.5H.5a.5.5 0 0 0 0 1h18a.5.5 0 0 0 0-1zM16.393 4.658c.318.106.607.507.607.842V19h-5V3.194l4.393 1.464zM2 4.5c0-.329.287-.714.602-.808L11 1.172V19H2V4.5z"></path>
        </symbol>
        <symbol viewBox="0 0 20 20" id="bubbles">
            <title>bubbles</title>
            <path d="M19.501 18H19.5c-1.341 0-2.734-.856-3.247-1.206A7.463 7.463 0 0 1 14.5 17c-1.437 0-2.795-.396-3.822-1.116C9.596 15.127 9 14.102 9 13s.596-2.127 1.678-2.884C11.706 9.397 13.063 9 14.5 9s2.795.396 3.822 1.116C19.404 10.873 20 11.898 20 13c0 .964-.461 1.877-1.306 2.597.082.231.339.727 1.145 1.535A.5.5 0 0 1 19.5 18zm-3.148-2.248c.108 0 .214.035.302.101.01.007.675.504 1.503.842-.544-.795-.53-1.248-.481-1.438a.504.504 0 0 1 .183-.273c.735-.552 1.139-1.256 1.139-1.984 0-.767-.444-1.5-1.251-2.065-.861-.603-2.015-.935-3.249-.935s-2.388.332-3.249.935c-.807.565-1.251 1.298-1.251 2.065s.444 1.5 1.251 2.065c.861.603 2.015.935 3.249.935.595 0 1.173-.077 1.718-.23a.516.516 0 0 1 .135-.018z"></path>
            <path d="M.5 19a.5.5 0 0 1-.257-.929 6.211 6.211 0 0 0 2.641-3.179C1.046 13.485 0 11.538 0 9.5c0-1.029.258-2.026.768-2.964.486-.894 1.18-1.695 2.061-2.381C4.616 2.765 6.985 1.999 9.5 1.999c2.278 0 4.481.644 6.202 1.814 1.735 1.179 2.871 2.811 3.199 4.595a.5.5 0 0 1-.983.181c-.279-1.519-1.266-2.921-2.778-3.949-1.557-1.058-3.56-1.641-5.64-1.641-4.687 0-8.5 2.916-8.5 6.5 0 1.815 1.005 3.562 2.756 4.792.172.121.25.336.196.539-.117.436-.515 1.633-1.58 2.788 1.302-.456 2.704-1.247 3.739-1.959a.499.499 0 0 1 .421-.069c.948.271 1.947.409 2.968.409a.5.5 0 0 1 0 1c-1.033 0-2.047-.129-3.016-.385a20.74 20.74 0 0 1-2.189 1.27c-1.488.74-2.764 1.115-3.794 1.115z"></path>
        </symbol>
        <symbol viewBox="0 0 20 20" id="calculator">
            <title>calculator</title>
            <path d="M16.5 20h-14c-.827 0-1.5-.673-1.5-1.5v-17C1 .673 1.673 0 2.5 0h14c.827 0 1.5.673 1.5 1.5v17c0 .827-.673 1.5-1.5 1.5zM2.5 1a.5.5 0 0 0-.5.5v17a.5.5 0 0 0 .5.5h14a.5.5 0 0 0 .5-.5v-17a.5.5 0 0 0-.5-.5h-14z"></path>
            <path d="M15.5 7h-12a.5.5 0 0 1-.5-.5v-4a.5.5 0 0 1 .5-.5h12a.5.5 0 0 1 .5.5v4a.5.5 0 0 1-.5.5zM4 6h11V3H4v3zM15.5 8h-12a.5.5 0 0 0-.5.5v9a.5.5 0 0 0 .5.5h12a.5.5 0 0 0 .5-.5v-9a.5.5 0 0 0-.5-.5zm-.5 3h-2V9h2v2zm-8 1h2v2H7v-2zm-1 2H4v-2h2v2zm1-3V9h2v2H7zm2 4v2H7v-2h2zm1 0h2v2h-2v-2zm2-1h-2v-2h2v2zm-2-3V9h2v2h-2zM6 9v2H4V9h2zm-2 6h2v2H4v-2zm9 2v-5h2v5h-2z"></path>
        </symbol>
        <symbol viewBox="0 0 20 20" id="cash-dollar">
            <title>cash-dollar</title>
            <path d="M18.5 18H.5a.5.5 0 0 1-.5-.5v-10A.5.5 0 0 1 .5 7h18a.5.5 0 0 1 .5.5v10a.5.5 0 0 1-.5.5zM1 17h17V8H1v9z"></path>
            <path d="M11.5 12H8v-1h3.5a.5.5 0 0 0 0-1H10v-.5a.5.5 0 0 0-1 0v.5H7.5a.5.5 0 0 0-.5.5v2a.5.5 0 0 0 .5.5H11v1H7.5a.5.5 0 0 0 0 1H9v.5a.5.5 0 0 0 1 0V15h1.5a.5.5 0 0 0 .5-.5v-2a.5.5 0 0 0-.5-.5zM17.5 6h-16a.5.5 0 0 1 0-1h16a.5.5 0 0 1 0 1zM16.5 4h-14a.5.5 0 0 1 0-1h14a.5.5 0 0 1 0 1z"></path>
        </symbol>
    </defs>
</svg>