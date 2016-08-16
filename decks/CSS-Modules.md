# [![CSS Modules](https://raw.githubusercontent.com/css-modules/logos/master/css-modules-logo.png)](http://glenmaddern.com/articles/css-modules)

<style> img { max-width: 100%; max-height: 50vh;} </style>

(you can use [plunker](http://plnkr.co/edit/8ayXQ34pyLvuPWYI3AlK?s=QOLiLFH2XSKCspUE&p=preview) to play around with CSS modules live)














### Enforce Isolation: Local by Default
```css
/* button.css */

.normal { }
```

…becomes…

```css
/* button.css */

:local .normal { }
```

…becomes…

```css
/* entry.css (dev) */

._components_pieces_button_index__normal { }
```

…becomes…

```css
/* entry.css (prod) */

.b9e49c4d { }
```
















### Enforce Isolation: Easy on the JS

```html
<button class="button-normal--inProgress"></button>
```

…becomes…

```js
import styles from './index.css'

buttonElem.outerHTML = `<button class=${styles.inProgress}>Submit</button>`
```

Importing the CSS in the JS ensures you don't have massive `index` files that chance missing or mis-ordering CSS imports. It also makes it easy to just `import myModule from './my-module.js'` to get both CSS and JS.




### Enforce Isolation: Think in Components
* Don't let your parents style their children
* Don't use `className` as a prop






### Naming

We don't need to worry about namespacing anymore!
```css
/* button.css */
.normal { /* all styles for Normal */ }
.disabled { /* all styles for Disabled */ }
.error { /* all styles for Error */ }
.inProgress { /* all styles for In Progress */ }
```












### Avoid the Cascade

**Don't do this**

```css
.normal {
  background: black
  color: white;
}

.inProgress::after {
  content: "loading…";
}
```

`class=${[styles.normal, styles.inProgress].join(' ')}`

**Do this**

`class=${styles.inProgress}`



### Avoid the Cascade: Think in components
* Always style single class names; don't style tags. This _will_ make your CSS easier to reason about.













### Don't break isolation!

**Don't do this**

```css
/* button.css */
.normal { background: black; color: white; }

/* alert-danger.css */
.button { background: red; }
```

```html
<Button className={styles.button}>Danger!</Button>
```

**Do this**

```css
/* button.css */
.text { color: white; }

.normal { composes: text; background: black }

.danger { composes: text; background: red; }
```

```html
<Button type="danger">Danger!</Button>
```















### [But… naming is hard! And descendant and tag selectors are easy!](https://github.com/css-modules/css-modules/issues/63)

Right, but:

→ prevent styles from being tied to your DOM structure

→ prevents you from accidentally styling subcomponents using tag selectors

→ <span style="background: yellow">ensures that CSS for a component is all in one place

→ <span style="background: yellow">prevents you from breaking isolation












### Follow these constraint!
* [x] global namespace ← _local by default_
* [x] dependencies ← _import into the JS_
* [x] dead code elimination ← _possible with a browserify transform_
* [x] minification ← _generated class names_
* [x] non-deterministic resolution ← _CSS files can only affect one component_
* [x] breaking isolation ← _Avoid the cascade, and you can't break isolation_






















# Composition

### Composition: Why?
This is the way to make all of these rules practical. Without composition, you're stuck with a lot of copy/paste.

### Composition: the way to avoid copy/paste

```css
.common {
  /* all the common styles you want */
}
.normal {
  composes: common;
  /* anything that only applies to Normal */
}
.disabled {
  composes: common;
  /* anything that only applies to Disabled */
}
.error {
  composes: common;
  /* anything that only applies to Error */
}
.inProgress {
  composes: common;
  /* anything that only applies to In Progress */
}
```

This is how you can do:

`class=${styles.inProgress}`

### Composition !== `@extends`

In Sass,

```css
.Button--common { /* font-sizes, padding, border-radius */ }
.Button--normal {
  @extends .Button--common;
  /* blue color, light blue background */
}
.Button--error {
  @extends .Button--common;
  /* red color, light red background */
}
```

…becomes

```css
.Button--common, .Button--normal, .Button--error { /* font-sizes, padding, border-radius */ }
.Button--normal { /* blue color, light blue background */ }
.Button--error { /* red color, light red background */ }
```

[Why this sucks](http://www.sitepoint.com/avoid-sass-extend/) (besides the bloated CSS).

Namely: Non-deterministic resolution is back! Or: `@extends` is DRY, but not safe.

### Composition == bug free

```css
.common { /* font-sizes, padding, border-radius */ }
.normal { composes: common; /* blue color, light blue background */ }
.error { composes: common; /* red color, light red background */ }
```

…becomes

```css
.components_submit_button__common__abc5436 { /* font-sizes, padding, border-radius */ }
.components_submit_button__normal__def6547 { /* blue color, light blue background */ }
.components_submit_button__error__1638bcd { /* red color, light red background */ }
```

```js
styles: {
  common: "components_submit_button__common__abc5436",
  normal: "components_submit_button__common__abc5436 components_submit_button__normal__def6547",
  error: "components_submit_button__common__abc5436 components_submit_button__error__1638bcd"
}
```

```html
<button class="components_submit_button__common__abc5436
               components_submit_button__normal__def6547">
  Submit
</button>
```

### Composition: share across files

```css
/* colors.css */
.primary {
  color: #720;
}
.secondary {
  color: #777;
}
```

```css
/* submit-button.css */
.common { /* font-sizes, padding, border-radius */ }
.normal {
  composes: common;
  composes: primary from "../shared/colors.css";
}
```

**Note**: when composing multiple classes from different files the order of appliance is undefined. Make sure to not define different values for the same property in multiple class names from different files when they are composed in a single class.

### Composition: extreme granularity

```css
.article {
  composes: flex vertical centered from "./layout.css";
}

.masthead {
  composes: serif bold 48pt centered from "./typography.css";
  composes: paragraph-margin-below from "./layout.css";
}

.body {
  composes: max720 paragraph-margin-below from "layout.css";
  composes: sans light paragraph-line-height from "./typography.css";
}
```












# `Variables`

```css
/* colors.css */
@value primary: #BF4040;
@value secondary: #1F4F7F;

.text-primary { color: primary; }
.text-secondary { color: secondary; }
```

```css
/* breakpoints.css */
@value small: (max-width: 599px);
@value medium: (min-width: 600px) and (max-width: 959px);
@value large: (min-width: 960px);
```

```css
/* my-component.css */
@value colors: "./colors.css";
@value primary, secondary from colors;
@value small as bp-small, large as bp-large from "./breakpoints.css";
.header { composes: text-primary from colors; box-shadow: 0 0 10px secondary; }
@media bp-small { .header { box-shadow: 0 0 4px secondary; } }
@media bp-large { .header { box-shadow: 0 0 20px secondary; } }
```


<div style="display: none">
#### Gets around the current limitations of CSS 4 variables

Using the current polyfill is limited by forcing all variables to be global, and not allowing them for use in media queries. `@values` solves it.
</div>



# The Constraints

## You can't override with composes

see: https://github.com/css-modules/css-modules/issues/14

The following is an error because you can't be sure which will override.

```css
.common {
  background: red;
}

.primary {
  composes: common;
  background: blue;
}
```

## No applying multiple classes

This is not good because you can't determine which class will override the other. It also makes it impossible to guarantee that changing the styles of the component won't accidentally break other components.

```html
<div className={[styles.primary, styles.focus]} />
```

You have to do this:

```html
<div className={[styles.primaryFocus]} />
```

Fortunately, `composes` makes this easy.

[Some other ideas](https://medium.com/brigade-engineering/don-t-pass-css-classes-between-components-e9f7ab192785):

* Keep the number of properties of your components to a minimum.
* Represent actual different ways a component can look or behave using properties.
* Split out your component into several smaller components that you can compose.

## Know when to break the rules
* Sometimes you break the rules. But know _why_ you're breaking them and please, please, please: leave a comment in the CSS saying why you're a special snowflake this one time.
* The one constraint you should (probably) never break: never style across components.
