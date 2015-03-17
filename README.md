# CSS Coding Guidelines

Pre-processors are great, but with [atomify](https://github.com/atomify/atomify)'s use of [rework](https://github.com/reworkcss/rework), we can use regular old CSS, but still get the best of preprocessors.

The core philosophy here:

* variables are amazing!
* mixins can be really useful
* nesting is actually an anti-pattern that leads to selector over-specificity.

Naming conventions are adapted from the work being done in the SUIT CSS framework. Which is to say, it relies on _structured class names_ and _meaningful hyphens_ (i.e., not using hyphens merely to separate words). This is to help work around the current limits of applying CSS to the DOM (i.e., the lack of style encapsulation) and to better communicate the relationships between classes.

This style guide is based on the structure provided by @fat in [Medium's Style Guide](https://gist.github.com/fat/a47b882eb5f84293c4ed).

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Organization](#organization)
- [JavaScript](#javascript)
- [Utilities](#utilities)
  - [u-utilityName](#u-utilityname)
- [Components](#components)
  - [Namespaces](#namespaces)
  - [ComponentName](#componentname)
  - [componentName--modifierName](#componentname--modifiername)
  - [componentName-descendantName](#componentname-descendantname)
  - [componentName.is-stateOfComponent and aria-*](#componentnameis-stateofcomponent-and-aria-)
- [`id` attribute](#id-attribute)
  - [Tag Names](#tag-names)
- [Selectors and Nesting](#selectors-and-nesting)
- [Variables](#variables)
  - [Colors](#colors)
  - [z-index scale [z-index]](#z-index-scale-z-index)
  - [Font Weight [font-weight]](#font-weight-font-weight)
  - [Line Height [line-height]](#line-height-line-height)
  - [Letter spacing [letter-spacing]](#letter-spacing-letter-spacing)
- [Units](#units)
  - [Percents](#percents)
  - [`em`s](#ems)
  - [`height`](#height)
- [Vendor Prefixes [vendor-prefixes]](#vendor-prefixes-vendor-prefixes)
- [Formatting](#formatting)
  - [Spacing](#spacing)
  - [Quotes](#quotes)
- [Performance](#performance)
  - [Specificity](#specificity)
- [Presentation-Specific vs Layout-Specific Styles](#presentation-specific-vs-layout-specific-styles)
- [Styles](#styles)
- [Media Queries](#media-queries)
- [Frameworks and Vendor Styles](#frameworks-and-vendor-styles)
- [Languages](#languages)
- [Credits](#credits)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Organization

Each component should have it's own style sheet. Even global styles get a component. CSS-only components are okay!

- Use a single `all.{styl,less,scss}` file and have it `@import` every other file
- Use a build tool to glob the styles directory

A few rules apply.

- Each component should take up its own file
- Styles applied globally to tag names, see [Tag Names](#tag-names), should be kept in a single file
- Where possible split **presentation-specific** styles from **layout-specific** styles. See below

## JavaScript

syntax: `data-js="<targetName>"`

Provide a clean separation of styles and JavaScript-specific points in the DOM to reduce the risk that changing the structure or theme of components will inadvertently affect any required JavaScript behaviour and complex functionality. It is not neccesarry to use them in every case, just think of them as a tool in your utility belt.

It's better to use `data` attributes than classes because datalist is easier to manipulate and it provides a very obvious differentation of styles vs JS hooks. In practice this looks like this:

```html
<a href="/login" class="btn btn-primary" data-js="login"></a>
```

```js
$('[data-js="login"]').on('click', onClick)
```

**Again, JavaScript-specific data attributes should not, under any circumstances, be styled.**

## Utilities

Utility classes are low-level structural and positional traits. Utilities can be applied directly to any element; multiple utilities can be used together; and utilities can be used alongside component classes.

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


### Namespaces

Components should _always_ be assigned a unique namespace prefix.

- The namespace can be as short as a single character, or as long as 5 characters
- Where possible, the namespace should be a meaningful shorthand
- In class names, the namespace must be followed by a single dash
- Views should be treated as individual components

Consider the following example, where we assigned `ddl` to a **drop down list** component. Take note of the class names.

##### Good

```css
.ddl-container {
  // ...
}

.ddl-item-list {
  // ...
}

.ddl-item {
  // ...
}
```

##### Bad

```css
.item-list {
  // ...
}

.dropdown-item-list {
  // ...
}

.xyz-item-list {
  // ...
}
```

See [Selectors and Nesting](#selectors-and-nesting) for information in regard to how styles should be overridden

### ComponentName

* The component's name must be written in camel case.
* As short as possible, but as long as necessary
  * _Don't abbreviate words carelessly_
* Name things consistently
* Meaningful description of the elements that should use it

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

### aria-*

It's best to use aria attributes instead of separate classes. Here's a list of aria states that you can use [via](http://slides.com/heydon/getting-nowhere-with-css-best-practices/#/41):

```
aria-busy
aria-grabbed
aria-invalid
aria-checked
aria-disabled
aria-expanded
aria-hidden
aria-invalid
aria-pressed
aria-selected
```

JS can add/remove these attrs. This means that the same state names can be used in multiple contexts, but every component must define its own styles for the state (as they are scoped to the component).

#### Right
```css
.tweet {
    display: none;
    background: rgb(0,0,0);
    font-size: 1rem;
    /* etc… */
}
.tweet[aria-expanded] {
    display: block;
}
```

```html
<article class="tweet" aria-expanded>
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

## `id` attribute

While the `id` attribute might be fine in HTML and JavaScript, it should be **avoided entirely** inside stylesheets. Few reasons.

- ID selectors are not reusable
- Priority nightmares
- [“Bad Code”, Dogmatism, etc.][1]

##### Good

```css
.ur-name {
  // ...
}
```

##### Bad

```css
#ur-name {
  // ...
}
```

Just assign a class name to the element.

### Tag Names

Tag names in selectors follow a few rules.

- Application level styles that are only overridden in a few places are okay to use tag name selectors
- [Not semantic][2]. **Avoid where possible**, use class names instead
- Fine to use when there's a ton of elements under the same namespace that need a small tweak
- **Don't overqualify** _(`a.foo`)_

##### Good

```css
button {
  padding: 5px;
  margin-right: 3px;
}

.ddl-button {
  background-color: #f00;
}
```

##### Bad

```css
.ddl-container button {
  background-color: #f00;
}
```

## Selectors and Nesting

Styles shouldn't need to be nested more than three _(four at worst)_ levels deep. This includes pseudo-selectors. If you find yourself going further, think about re-organizing your rules _(either the specificity needed, or the layout of the nesting)_.

##### Good

```css
.sg-title-icon:before {
  // ...
}

.dg-container .sg-title {
  font-size: 1.1em; // larger segment title inside dialogs
}
```

##### Bad

```css
.dg-container .sg-container .sg-title {
  font-size: 1.1em;
}

.dg-container .sg-title span:before {
  // ...
}
```

If a component needs to be different within another component, these rules apply.

- Where possible, give a class name using the parent namespace to the child component
- If that's not possible, then use a nested selector

Suppose you have a User List component `.ul-*` and a User Card component `.uc-*`.

##### Good

```html
<div class='ul-container'>
  <div class='uc-container'>
    <span class='uc-name ul-card-name'>John Doe</span>
  </div>
</div>
```

```css
.ul-card-name {
  // ...
}
```

##### Okay

```html
<div class='ul-container'>
  <div class='uc-container'>
    <span class='uc-name'>John Doe</span>
  </div>
</div>
```

```css
.ul-container .uc-name {
  // ...
}
```

##### Bad

```html
<div class='ul-container'>
  <div class='uc-container'>
    <span class='uc-name uc-name-in-ul'>John Doe</span>
  </div>
</div>
```

```css
.uc-name-in-ul {
  // ...
}
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

CSS rules should be comma separated but live on new lines:

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

CSS blocks should be separated by a single line. This is to allow users to jump between styles in the same way they jump between paragraphs.

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

## Presentation-Specific vs Layout-Specific Styles

Presentation-Specific styles are those that **only alter the visual design** of the element, but don't change its dimensions or position in a meaningful way. The examples below are presentation-specific.

- Rules such as `color`, `font-weight`, or `font-variant`
- Rules that animate other properties
- `font-size` is not considered a meaningful dimension change
- `max-width` and `max-height` may fit either category, but it's generally reasonable to consider them presentation-specific

Layout-Specific Styles are those that change the dimensions or positioning of DOM elements. These are mostly layout-specific.

- Rules such as `margin` or `padding`
- `width`, and `height`
- The element's `position`
- `z-index`, definitely

Where possible, it's suggested to explicitly split styles into these two categories. The explicit differentiation could be made in a few different ways.

- _(bad)_ No differentiation
- _(decent)_ Layout-specific first, presentation-specific later
- _(good)_ A line-break between both categories
- _(better)_ Split in subsequent style declarations using the same selector

##### Good

```css
.foo {
  position: fixed;
  top: 8px;
  right: 8px;
  padding: 2px;
  font-weight: bold;
  background-color: #333;
  color: #f00;
}
```

```css
.foo {
  position: fixed;
  top: 8px;
  right: 8px;
  padding: 2px;

  font-weight: bold;
  background-color: #333;
  color: #f00;
}
```

##### Bad

```css
.foo {
  font-weight: bold;
  background-color: #333;
  color: #f00;
  position: fixed;
  top: 8px;
  right: 8px;
  padding: 2px;
}

.foo {
  right: 8px;
  color: #f00;
  padding: 2px;
  top: 8px;
  background-color: #333;
  font-weight: bold;
  position: fixed;
}
```

## Styles

These rules apply to your CSS property values

- If the value of a property is `0`, do not specify units
- The `!important` rule should be aggressively avoided.
  - Keep style rules in a sensible order
  - Compose styles to dissipate the need for an `!important` rule
  - Fine to use in limited cases
    - Overlays
    - Declarations of the `display: none !important;` type
- Keep `z-index` levels in variables in a single file. **Avoids confusion** about what level should be given to an element, and arbitrarily-high `999`-style values
- Dislike magic numbers, use variables. Prefer global vars over component-specific vars.
- Avoid mixing units, unless in `calc`
- Prefer `rgb` over `rgba` over hex.
- Unit-less `line-height` is preferred because it does not inherit a percentage value of its parent element, but instead is based on a multiplier of the `font-size`.

##### Good

```css
.btn {
  color: #222;
}

.btn-red {
  color: #f00;
}
```

##### Bad

```css
.btn-red {
  color: #f00 !important;
}

.btn {
  color: #222;
}
```

## Media Queries

> If you are reading this, I salute you. You're _almost as boring_ as I am. I'm more boring because **I actually wrote the damn thing**. It's not a contest, though.

A few rules apply to media queries.

- Settle for a few _(2-3)_ breakpoints and use those only
- Don't wrap entire stylesheets in media queries
- Approach your styles in a [Mobile First][5] manner. Generally you add more things as you get more real state. **Mobile First** logically follows

##### Good

```css
.co-field {
  width: 120px;
}

@media only screen and (min-width: 768px) {
  .co-field {
    width: 400px;
    color: #f00;
  }
}
```

##### Bad

```css
.co-field {
  width: 400px;
  color: #f00;
}

@media only screen and (max-width: 768px) {
  .co-field {
    width: 120px;
    color: initial;
  }
}
```

## Frameworks and Vendor Styles

You should shy away from all of these. A few rules apply.

- **Stay away from frameworks**
- Use [`normalize.css`][4] if you want
- Vendor styles, such as those required by external components are okay, and they should come before you define any of your own styles

## Languages

Some rules apply to stylesheet, regardless of language.

- Use a pre-processor language where possible. Rework is probably the best.
- Use soft-tabs with a two space indent
- One line per selector
- One _(or more)_ line(s) per property declaration
  - Long, comma-separated property values _(such as collections of gradients or shadows)_ can be arranged across multiple lines in an effort to improve readability and produce more useful diffs.
- Comments that refer to selector blocks should be on a separate line immediately before the block to which they refer
- Use a plugin such as **EditorConfig** to get rid of trailing spaces

##### Good

```css
.foo {
  color: #f00;
}

.foo
, .bar
, .baz {
  color: #f00;
}
```

##### Bad

```css
.foo {
    color: #foo;
}

.foo, .bar, .baz {
  color: #f00;
}

.foo {
  color: red;
}
```

## Credits
Some ideas from [bevacqua](https://github.com/bevacqua/css) and @fat in [Medium's Style Guide](https://gist.github.com/fat/a47b882eb5f84293c4ed).

[1]: http://css-tricks.com/bad-code-dogmatism-etc/ "Bad Code, Dogmatism, etc"
[2]: http://smacss.com/ "SMACSS modular CSS architecture"
[3]: https://github.com/bevacqua/css/issues "bevacqua/css Issues on GitHub"
[4]: http://necolas.github.io/normalize.css/ "A modern, HTML5-ready alternative to CSS resets"
[5]: http://bradfrostweb.com/blog/mobile/the-many-faces-of-mobile-first/ "The Many Faces of Mobile First"
[6]: https://github.com/bevacqua/js
