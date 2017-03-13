---
layout: post
title:  "Front-end Color Organisation"
date:   2017-03-12
categories: sass colors
---

Keeping colors consistent across an application is difficult, I thought I would share some patterns that I use.

When I see a new color in a design I begin with assigning it as a global sass vairable. Another option would be to use a [sass map](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps) for colors, making looping possible (I am considering changing to this approach).


## Variables
The basic building block for the color system is the global variable. Using a consistent post-fix and a unique identifier.
```css
$color-grey-elephant: #F1F1F1;
$color-red-kickflip: #e74c18;
$color-green-leaf: #17ae00;
```
Note: The syntax in this post is in Sass.

The post-fix "color" identifies the color as a color, next "$color-[green etc.]" gives some indication to what color its is, then lastly the name is just a any unique string, providing some memorbility by providing it with a unqiue/ridicolous name. I have been called out multiple times on how ridicous my naming can get on occasion, but at least it is memorable. These color names can then be shared with designers I work with, so that everyone has the same name to reference for the color. I am still exprimenting with ways to share these colors to people that do not code, one attempt I have experimented with is to regex all the hex codes in a file and then use those hex codes to build a static html file. (see if I can find a pic for this one).

## Aliases

The second most important aspect to the color system is aliases. I could spend the energy to inform everyone that "$color-green-leaf" is used on all "Edit" buttons, or give the colors secondary names that describe exactly what it is used for.

```css
$color-alias-edit-button: $color-green-leaf;
$color-alias-delete-icons: $color-red-kickflip;
$color-alias-external-hyperlinks: $color-green-leaf;
```
Its clear what each color is used for, and others can reference the code for the color alias. It also helps prevent others from creating similar colors for the same purpose, so you wont end up with 3 similar shades of green for your edit buttons.

## Selectors

Having the colors avaiable in sass is great, having them avaliable for direct use in the html is handy too. To have easy access to each color and color alias I then create a class of the same name for each.

```css
.color-grey-elephant { color: $color-grey-elephant; }
.color-alias-edit-button { color: $color-alias-edit-button; }
```

This can become less tedious by looping to producing these classes, one approach could be to store the colors in a [sass map](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps). 

When I want to use the color in the html, I can then use it directly as a class.

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


