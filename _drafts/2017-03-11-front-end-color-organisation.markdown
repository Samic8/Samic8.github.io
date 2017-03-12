---
layout: post
title:  "Front-end Color Organisation"
date:   2017-03-12
categories: sass colors
---

Keeping colors consistent accross your application is difficult, I thought I would share some patterns that have helped me.

When I see a new color in a design I begin with assigning it as a global sass vairable (could change this to maps). This is for sass but im guessing could be applied to similar frameworks.

Variables

```css
$color-grey-elephant: #F1F1F1;
$color-red-kickflip: #e74c18;
$color-green-leaf: #17ae00;
```
The post-fix "color" identifies it as a color, next "$color-[green etc.]" gives some indication to what color its is, then last part of the variable name is just a any unique word. So when seeing a color name in the code this provides the befit of 1. Knowing its a color, 2. Knowing what type(?) of color it is, 3. providing some memorbility by providing it with a unqiue/ridicolous name. I have been called out multiple times on how ridicous my naming is on occasion, but at least it is memorable. These color names can then be shared with designers so that everyone has the same name for the color. I am still exprimenting with ways to share these colors to people that do not code, one attempt was to regex all the hex codes in a file and then use those hex codes to build a static html file. (see if I can find a pic for this one).

Aliases

The second most importart aspect to the color system is aliases. You could spend the time to inform everyone that "$color-green-leaf" is used on all "Edit" buttons, or give your colors secondary names that describe exactly what it is used for.

```css
$color-alias-edit-button: $color-green-leaf;
$color-alias-delete-icons: $color-red-kickflip;
$color-alias-external-hyperlinks: $color-green-leaf;
```
Its clear what each color is used for, and others can reference the code for the color alias. It also helps prevent others from creating similar colors for the same purpose, so you wont end up with 3 similar shades of green for your edit buttons.

selectors

Having the colors avaiable in your sass is great, having them avaliable for direct use in your html is handy too. For each color and color alias I then create a class of the same name

```css
.color-grey-elephant { color: $color-grey-elephant; }
.color-alias-edit-button { color: $color-alias-edit-button; }
```

This can be less tedious by programictly producing these classes using a loop of some kind by storing your colors in a [sass map](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps).

``` html
<a class="button color-alias-edit-button">Edit</a>
```

Its most likely that we want the color of the buttons background to be green, but the class that we have applied will only change the text color by default. This is where the use of the property value ["currentColor"](https://developer.mozilla.org/en/docs/Web/CSS/color_value#currentcolor_keyword) becomes very useful. To get the button to appear with a green background we could add this to our button class.

```css
.button {
	display: inline-block;
	padding: 10px 5px;
	border-radius: 2px;

	background: currentColor;
}
```

Now we have a green background. A warning with using currentColor in this context, is that the buttons text is now green aswell. To fix this we can use another element within our button and make sure it does not inherit its color from its parent.

``` html
<a class="button color-alias-edit-button">
	<span class="color-grey-elephant">Edit</span>
</a>
```

Aliases in Javascript

I have found it useful to keep a list of color aliases in the javascript, for use in graphs. That way the sass still owns the colors, but the js has a reference to the class name which it can then apply when needed to elements. This saves use from duplication, and when the colors change in the sass it will be reflected in the JS applied verisions aswell. In the following example the key is the type of data being graphed.

```javascript
const classColorAliases = {
	people: 'color-alias-graph-people',
	dogs: 'color-alias-graph-dogs',
	cats: 'color-alias-graph-cats'
};
```


