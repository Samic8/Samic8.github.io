---
layout: post
title:  "Vendor Prefixing mixin"
date:   2015-08-09 20:20:30
categories: scss Sass mixin vendor prefixer prefix moz o ms 
---

A mixin to quickly create vendor prefixers on the fly!
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

<br>
The simplest use of the prefixer mixin

Input
{% highlight scss %}
.flexbox-container{
    @include prefixer(display, flex);
}
{% endhighlight %}
Output 
{% highlight scss %}
.flexbox-container {
    -webkit-display: flex;
    -moz-display: flex;
    -ms-display: flex;
    -o-display: flex;
    display: flex;
}
{% endhighlight %}

<br>
Specifiy particular vendor prefixes

Input
{% highlight scss %}
.flexbox-container{
    @include prefixer(display, flex, moz webkit spec);
}
{% endhighlight %}
Output
{% highlight scss %}
.flexbox-container {
    -moz-display: flex;
    -webkit-display: flex;
    display: flex;
}
{% endhighlight %}


