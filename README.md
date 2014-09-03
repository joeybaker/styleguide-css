# CSS Coding Guidelines

Pre-processors are great, but with [atomify](https://github.com/atomify/atomify)'s use of [rework](https://github.com/reworkcss/rework), we can use regular old CSS, but still get the best of preprocessors.

The core philosophy here:

* variables are amazing!
* mixins can be really useful
* nesting is actually an anti-pattern that leads to selector over-specificity.

Naming conventions are adapted from the work being done in the SUIT CSS framework. Which is to say, it relies on _structured class names_ and _meaningful hyphens_ (i.e., not using hyphens merely to separate words). This is to help work around the current limits of applying CSS to the DOM (i.e., the lack of style encapsulation) and to better communicate the relationships between classes.

This style guide is based on the structure provided by @fat in [Medium's Style Guide](https://gist.github.com/fat/a47b882eb5f84293c4ed).

## Table of Contents
<!-- MarkdownTOC -->

- [JavaScript](#javascript)
- [Utilities](#utilities)
  - [u-utilityName](#u-utilityname)
- [Components](#components)
  - [ComponentName](#componentname)
  - [componentName--modifierName](#componentname--modifiername)
  - [componentName-descendantName](#componentname-descendantname)
  - [componentName.is-stateOfComponent](#componentname.is-stateofcomponent)
- [Variables](#variables)
  - [Colors](#colors)
  - [z-index scale](#z-index)
  - [Font Weight](#font-weight)
  - [Line Height](#line-height)
  - [Letter spacing](#letter-spacing)
- [Units](#units)
  - [Percents](#percents)
  - [`em`s](#ems)
  - [`height`](#height)
- [Vendor Prefixes](#vendor-prefixes)
- [Formatting](#formatting)
  - [Spacing](#spacing)
  - [Quotes](#quotes)
- [Performance](#performance)
  - [Specificity](#specificity)

<!-- /MarkdownTOC -->

## JavaScript

syntax: `js-<targetName>`

JavaScript-specific classes reduce the risk that changing the structure or theme of components will inadvertently affect any required JavaScript behaviour and complex functionality. It is not neccesarry to use them in every case, just think of them as a tool in your utility belt. If you are creating a class, which you dont intend to use for styling, but instead only as a selector in JavaScript, you should probably be adding the `js-` prefix. In practice this looks like this:

```html
<a href="/login" class="btn btn-primary js-login"></a>
```

**Again, JavaScript-specific classes should not, under any circumstances, be styled.**

## Utilities

Medium's utility classes are low-level structural and positional traits. Utilities can be applied directly to any element; multiple utilities can be used together; and utilities can be used alongside component classes.

Utilities exist because certain CSS properties and patterns are used frequently. For example: floats, containing floats, vertical alignment, text truncation. Relying on utilities can help to reduce repetition and provide consistent implementations. They also act as a philosophical alternative to functional (i.e. non-polyfill) mixins.


```html
<div class="u-clearfix">
  <p class="u-textTruncate">{$text}</p>
  <img class="u-pullLeft" src="{$src}" alt="">
  <img class="u-pullLeft" src="{$src}" alt="">
  <img class="u-pullLeft" src="{$src}" alt="">
</div>
```

### u-utilityName

Syntax: `u-<utilityName>`

Utilities must use a camel case name, prefixed with a `u` namespace. What follows is an example of how various utilities can be used to create a simple structure within a component.

```html
<div class="u-clearfix">
  <a class="u-pullLeft" href="{$url}">
    <img class="u-block" src="{$src}" alt="">
  </a>
  <p class="u-sizeFill u-textBreak">
    …
  </p>
</div>
```

## Components

Syntax: `<componentName>[--modifierName|-descendantName]`

Component driven development offers several benefits when reading and writing HTML and CSS:

* It helps to distinguish between the classes for the root of the component, descendant elements, and modifications.
* It keeps the specificity of selectors low.
* It helps to decouple presentation semantics from document semantics.

You can think of components as custom elements that enclose specific semantics, styling, and behaviour.


### ComponentName

The component's name must be written in camel case.

```css
.myComponent { /* … */ }
```

```html
<article class="myComponent">
  …
</article>
```


### componentName--modifierName

A component modifier is a class that modifies the presentation of the base component in some form. Modifier names must be written in camel case and be separated from the component name by two hyphens. The class should be included in the HTML _in addition_ to the base component class.

```css
/* Core button */
.btn { /* … */ }
/* Default button style */
.btn--default { /* … */ }
```

```html
<button class="btn btn--primary">…</button>
```
### componentName-descendantName

A component descendant is a class that is attached to a descendant node of a component. It's responsible for applying presentation directly to the descendant on behalf of a particular component. Descendant names must be written in camel case.

```html
<article class="tweet">
  <header class="tweet-header">
    <img class="tweet-avatar" src="{$src}" alt="{$alt}">
    …
  </header>
  <div class="tweet-body">
    …
  </div>
</article>
```

### componentName.is-stateOfComponent

Use `is-stateName` for state-based modifications of components. The state name must be Camel case. **Never style these classes directly; they should always be used as an adjoining class.**

JS can add/remove these classes. This means that the same state names can be used in multiple contexts, but every component must define its own styles for the state (as they are scoped to the component).

#### Right
```css
.tweet {
    display: none;
    background: rgb(0,0,0);
    font-size: 1rem;
    /* etc… */
}
.tweet.is-expanded {
    display: block;
}
```

```html
<article class="tweet is-expanded">
  …
</article>
```

#### Wrong
```css
.tweet {
    display: none;
}
.tweet.is-expanded {
    display: block;
    background: rgb(0,0,0);
    font-size: 1rem;
    /* etc… */
}
```

```html
<article class="tweet is-expanded">
  …
</article>
```

## Variables

Syntax: `<property>-<value>[--componentName]`

Variable names in our CSS are also strictly structured. This syntax provides strong associations between property, use, and component.

The following variable defintion is a color property, with the value grayLight, for use with the highlightMenu component.

```CSS
@color-grayLight--highlightMenu: rgb(51, 51, 50);
```

### Colors

When implementing feature styles, **you should only be using color variables provided by `variables/colors.css`.**

When adding a color variable to `colors.css`, using RGB and RGBA color units are preferred over hex, named, HSL, or HSLA values.

**Right:**
```css
rgb(50, 50, 50);
rgba(50, 50, 50, 0.2);
```

**Wrong:**
```css
#FFF;
#FFFFFF;
white;
hsl(120, 100%, 50%);
hsla(120, 100%, 50%, 1);
```

### z-index scale [z-index]

Please use the z-index scale defined in `variables/z-index.css`.

`@zIndex-1 - @zIndex-9` are provided. Nothing should be higher then `@zIndex-9`.


### Font Weight [font-weight]

With the additional support of web fonts `font-weight` plays a more important role than it once did. Different font weights will render typefaces specifically created for that weight, unlike the old days where `bold` could be just an algorithm to fatten a typeface. Use the numerical value of `font-weight` to enable the best representation of a typeface. The following table is a guide:

Raw font weights should be avoided. Instead, use the appropriate font mixin: `.font-sansI7, .font-sansN7, etc.`

The suffix defines the weight and style:

```CSS
N = normal
I = italic
4 = normal font-weight
7 = bold font-weight
```

Refer to `type.css` for type size, letter-spacing, and line height. Raw sizes, spaces, and line heights should be avoided outside of `type.css`.


```CSS
ex:

@fontSize-micro
@fontSize-smallest
@fontSize-smaller
@fontSize-small
@fontSize-base
@fontSize-large
@fontSize-larger
@fontSize-largest
@fontSize-jumbo
```

See [Mozilla Developer Network — font-weight](https://developer.mozilla.org/en/CSS/font-weight) for further reading.


### Line Height [line-height]

`type.css` also provides a line height scale. This should be used for blocks of text.


```CSS
ex:

@lineHeight-tightest
@lineHeight-tighter
@lineHeight-tight
@lineHeight-baseSans
@lineHeight-base
@lineHeight-loose
@lineHeight-looser
```

Alternatively, when using line height to vertically center a single line of text, be sure to set the line height to the height of the container - 1.

```CSS
.btn {
  height: 50px;
  line-height: 49px;
}
```

### Letter spacing [letter-spacing]

Letter spacing should also be controlled with the following var scale.

```CSS
@letterSpacing-tightest
@letterSpacing-tighter
@letterSpacing-tight
@letterSpacing-normal
@letterSpacing-loose
@letterSpacing-looser
```

## Units

The web should be fluid by default. Locking your layout to specific sizes will make this goal harder. So, use units that allow fluidity.

### Percents
Unless dealing directly with fonts or media quires, `%` is usually the best unit to use. `vw` and `vh` are also very handy.

### `em`s
Always prefer using `em` over `px`. This allows your layout to adapt to font-size changes smoothly.

This is especially true when setting `width`, `font-size`, `padding`, and `margin`.

### `height`
Use `height` with caution. Pages are layed out horizontally, limiting your height will nearly always lead to problems.

## Vendor Prefixes [vendor-prefixes]

Avoid vendor prefixes where possible. Only support current version of browsers and two versions back. Use autoprefixer to automatically generate the correct prefixes for you.

## Formatting

The following are some high level page formatting style rules.

### Spacing

CSS rules should be comma seperated but live on new lines:

**Right:**
```css
.content
, .content-edit {
  …
}
```

**Wrong:**
```css
.content, .content-edit {
  …
}
```

CSS blocks should be seperated by a single line. This is to allow users to jump between styles in the same way they jump between paragraphs.

**Right:**
```css
.content {
  …
}

.content-edit {
  …
}
```

**Wrong:**
```css
.content {
  …
}
.content-edit {
  …
}
```


### Quotes

Quotes are technically optional in CSS. Use double quotes as it is visually clearer that the string is not a selector or a style property.

**Right:**
```css
background-image: url("/img/you.jpg");
font-family: "Helvetica Neue Light", "Helvetica Neue", Helvetica, Arial;
```

**Wrong:**
```css
background-image: url(/img/you.jpg);
font-family: Helvetica Neue Light, Helvetica Neue, Helvetica, Arial;
```

## Performance

### Specificity

Although in the name (cascading style sheets) cascading can introduce unnecessary performance overhead for applying styles. Take the following example:

```css
ul.user-list li span a:hover { color: red; }
```

Styles are resolved during the renderer's layout pass. The selectors are resolved right to left, exiting when it has been detected the selector does not match. Therefore, in this example every a tag has to be inspected to see if it resides inside a span and a list. As you can imagine this requires a lot of DOM walking and and for large documents can cause a significant increase in the layout time. For further reading checkout: https://developers.google.com/speed/docs/best-practices/rendering#UseEfficientCSSSelectors

If we know we want to give all `a` elements inside the `.user-list` red on hover we can simplify this style to:

```css
.user-list > a:hover {
  color: red;
}
```

If we want to only style specific `a` elements inside `.user-list` we can give them a specific class:

```css
.user-list > .link-primary:hover {
  color: red;
}
```
