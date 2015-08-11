---
layout: post
title:  "Vendor Prefixing mixin"
date:   2015-08-09 20:20:30
categories: scss Sass mixin vendor prefixer prefix moz o ms 
---

**Edit:** Since writing this post I have now realized that this prefixer mixin does not have much of a use case. After prefixing a entire project with the prefixer mixin, more specifically the flex-box properties. I was only viewing the project in chrome until I had completed it, once I viewed the page in safari it was more then a little broken. 

The prefixer mixin was producing
{% highlight scss %}
.flexbox-container {
    -moz-display: flex;
    -webkit-display: flex;
    -o-display: flex;
    display: flex;
}
{% endhighlight %}
And the correct vendor prefixers are
{% highlight scss %}
    display: -webkit-box;
    display: -moz-box:
    display: -ms-flexbox:
    display: -webkit-flex;
    display: flex;
{% endhighlight %}

My current solution is to create separate mixins for each flex-box property. [Compass](http://compass-style.org/) also approaches [flex-box prefixing](http://compass-style.org/reference/compass/css3/flexbox/) in a very similar way.

Original Post
---

####A mixin to quickly create vendor prefixer's on the fly!####

{% highlight scss %}
@mixin prefixer($property, $value, $prefixes: webkit moz ms o spec) {
    @each $prefix in $prefixes {
        @if $prefix == 'webkit' or $prefix == 'moz' or $prefix == 'ms' or $prefix == 'o'{
            -#{$prefix}-#{$property}: $value;
        } 
        @elseif $prefix == 'spec'{
            #{$property}: $value;
        }
        @else{
            @warn '#{$prefix} is not a valid vendor prefix.'
        }
     }
}
{% endhighlight %}

####The simplest use of the prefixer mixin####

**Input**
{% highlight scss %}
.flexbox-container{
    @include prefixer(display, flex);
}
{% endhighlight %}
**Output**
{% highlight scss %}
.flexbox-container {
    -webkit-display: flex;
    -moz-display: flex;
    -ms-display: flex;
    -o-display: flex;
    display: flex;
}
{% endhighlight %}

####Specifiy particular vendor prefixes####

**Input**
{% highlight scss %}
.flexbox-container{
    @include prefixer(display, flex, moz webkit spec);
}
{% endhighlight %}
**Output**
{% highlight scss %}
.flexbox-container {
    -moz-display: flex;
    -webkit-display: flex;
    display: flex;
}
{% endhighlight %}