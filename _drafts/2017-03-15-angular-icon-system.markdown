---
layout: post
title:  "Angular Icon System"
date:   2017-03-15
categories: angular icons angular1
---

Here is the basics behind a angular icon system that I use. It keeps icon naming consistent and the html needed to generate a icon short.

## Icon
A prerequisite for this icon system is that every icon has a unique name. Our icon system relies on a single svg with multiple symbols, we then [`use`](linkToSvgUse) the symbol in one or more places.

{% highlight html %}
<svg style="position: absolute; width: 0; height: 0; overflow: hidden" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
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
(find out what xmlns and xmlns:xlink are used for and why they are needed)

This svg sits within the body of our html. There is another approach using [externally referenced svg files](https://css-tricks.com/svg-use-with-external-reference-take-2/). (maybe side note here about our caching).

I am going to skip over how we got to this point, the svg is produced by our 'icon build system' which I will explore in another post fully, but the build system allows us to dump all of our icons as a single svg into a directory, the build system then collects all of those icons and injects them into the body.

Once we have the symbol injected into our html we can [`use`](linkToSvgUse) our svg's. At this point we have not used angular at all (explain the benefits of using angular here).
{% highlight html %}
<svg class="icon">
    <use xlink:href="#apartment"></use>
</svg>
{% endhighlight %}
(explain xlink href)
We are referencing the #apartment svg that is in our svg defs. The above example produces <svg class="icon" style="width: 20px; height: 20px; display: inline-block;"><use xlink:href="#apartment"></use></svg>

Now that we have got how the vanilla version of this icon system works we can sprinkle in some angular. We are going to create a angular component which uses a variation of the above html as its template. Instead of the hard coded '#apartment' we use a injected string `iconName` from the svgIcon controller.
{% highlight javascript %}
.component('svgIcon', {
    template: '<svg class="icon">
                  <use xlink:href="{$ctrl.iconName}"></use>
              </svg>',
});
{% endhighlight %}

// TODO find out how to escape double brackets and use in the example above

Use iconName as  our binding, icon name references the symbol name directly from our svg defs.
{% highlight javascript %}
.component('svgIcon', {
    template: '<svg class="icon">
                  <use xlink:href="{$ctrl.iconName}"></use>
              </svg>',
    bindings: {
        iconName: '<',
    },
    controller: function (){}
});
{% endhighlight %}

Then we can use the component
{% highlight html %}
<svg-icon icon-name="'apartment'"><svg-icon>
{% endhighlight %}
Assuming we have our modules that contain this component setup correctly this will produce the same apartment icon <svg class="icon" style="width: 20px; height: 20px; display: inline-block;"><use xlink:href="#apartment"></use></svg>

Here is a working example of all of the concepts we have covered so far. Assumed knowledge here is the [`$onChanges` life cycle hook](https://blog.thoughtram.io/angularjs/2016/03/29/exploring-angular-1.5-lifecycle-hooks.html#onchanges) and [`ng-style`](https://docs.angularjs.org/api/ng/directive/ngStyle) used to set our icon size.

<p data-height="400" data-theme-id="0" data-slug-hash="xqXzqa" data-default-tab="html,result" data-user="Samic8" data-embed-version="2" data-pen-title="Angular Icon System (Icon)" class="codepen">See the Pen <a href="https://codepen.io/Samic8/pen/xqXzqa/">Angular Icon System (Icon)</a> by Sam Dawson (<a href="http://codepen.io/Samic8">@Samic8</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Aliases
<p data-height="400" data-theme-id="0" data-slug-hash="VpMdzO" data-default-tab="html,result" data-user="Samic8" data-embed-version="2" data-pen-title="Angular Icon System (Aliases)" class="codepen">See the Pen <a href="https://codepen.io/Samic8/pen/VpMdzO/">Angular Icon System (Aliases)</a> by Sam Dawson (<a href="http://codepen.io/Samic8">@Samic8</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Repeat
<p data-height="400" data-theme-id="0" data-slug-hash="MpvGjy" data-default-tab="html,result" data-user="Samic8" data-embed-version="2" data-pen-title="Angular Icon System (Complete)" class="codepen">See the Pen <a href="https://codepen.io/Samic8/pen/MpvGjy/">Angular Icon System (Repeat)</a> by Sam Dawson (<a href="http://codepen.io/Samic8">@Samic8</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

TODOs
Injecting svg defs in to the file
External svg defs
Link about using color aliases from other post
add links and highlight keys words
assumed knowledge. angular components etc.

Try to keep short, I know I dont have a long attention span, maybe just touch on some of these topics and explore them furthur in another post

<svg style="position: absolute; width: 0; height: 0; overflow: hidden" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
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
    </defs>
</svg>