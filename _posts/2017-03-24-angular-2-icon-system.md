---
layout: post
title:  "Angular 2 Icon System"
date:   2017-03-24 +1000
categories: angular icons angular2
---

Here is the ["Angular Icon System"]({% post_url 2017-03-21-angular-icon-system %}) rebuilt for angular 2. Converting it to angular 2 did provide some syntactic sugar, the component otherwise remains mostly the same.

Link to [Plunker](https://plnkr.co/edit/ZKnGIvvYUBfwFag5BqnX?p=info) example

### svg-icon.component.ts

{% highlight typescript %}
import { Component, OnChanges, Input } from '@angular/core';
import { NgStyle } from '@angular/common/index';

@Component({
  selector: 'svg-icon',
  template: 
    `<svg class="icon" [ngStyle]="svgStyle">
      <use attr.xlink:href="#{{svgSymbolId}}"></use>
    </svg>`,
  styleUrls: ['./svg-icon.component.css']
})

export class SvgIconComponent implements OnChanges {
  @Input() iconName: string;
  @Input() iconSize: number;
  
  svgSymbolId: string;
  svgStyle: object;
  
  ngOnChanges(changes: SimpleChanges) {
    if (changes.iconName) {
      this.svgSymbolId = changes.iconName.currentValue;
    }
    
    if (changes.iconSize) {
      this.svgStyle = { 'font-size': changes.iconSize.currentValue };
    }
  }
}
{% endhighlight %}

### svg-icon.component.css

{% highlight css %}
.icon {
  fill: currentColor;
  width: 1em;
  height: 1em;
}
{% endhighlight %}

### HTML usage

{% highlight html %}
<svg-icon [iconName]="'apartment'" [iconSize]="40"><svg-icon>
{% endhighlight %}