# CSS The Bad Parts











# CSS in a Non-Trivial Codebase

<div style="display: none">
* 4 minor problems ← _There are work-arounds_
* 2 hard problems ← _You need a tool to solve these_
</div>












## Global namespace

```css
button {
  background: blue;
  color: white;
  border: 1px solid #444;
  border-radius: 3px;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.4) inset;
}

button:active {
  background: navy;
  border-color: black;
  color: #eee;
}
```

<div style="display: none">
We've just introduced two new global variables! This means we can never use the `.button` class again. Worse: if we do, nothing will error. CSS is happy to let you override current behavior, even if it's an accident.

Of course, you can namespace your classes. Plenty of systems have developed to do this:

* OOCSS
* SMACSS
* BEM
* SUIT
* even inline styles
</div>











## So, namespace

```html
<div class="page my-page">

  <header class="header">
    <h1 class="heading header__heading">...</h1>
  </header>

  <article class="post">
    <header class="post__header">
      <h1 class="heading post__heading">Hello World</h1>
    </header>
    <section class="post__content">...</section>
  </article>

  <footer class="footer">...</footer>

</div>
```
[via](http://markdalgleish.github.io/presentation-the-case-for-css-modules/#15)

<div style="display: none">
This absolutely gets around the problem, but it's error prone. Ideally, you'd have a system were styles were automatically scoped to their component and their component only.
</div>











## Dependencies

```css
@import "../../pieces/button";
@import "../../pieces/button-group";
@import "../../pieces/card";
@import "../../components/form-user-switch-branch";

/* Styles for the AdminUserDetail component */
.admin-user-detail .card {
  margin: 5px 10px 10px 10px;
}

.admin-user-detail .alert {
  margin-bottom: 10px;
}
```

<div style="display: none">
Of course, it's easy to forget to import the button. And, that might work if something else requires the button already … until you remove that other thing. And there's no way for the build to know that you're missing a dependency!
</div>














## Dead Code Elimination

```css
.btn.btn-primary {
  background-color: var(--color-grey70);
  color: var(--color-grey30);
  border: 1px solid var(--color-black10);
  box-shadow:         0px 1px 1px 0px rgba(255,255,255,0.20), inset 0px 1px 1px 0px rgba(255,255,255,0.30);
}

.btn.btn-primary:hover, .btn.btn-primary:focus {
    background-color: var(--color-grey70);
}

.btn.btn-primary:active, .btn.btn-info:active {
  box-shadow:         0px 1px 1px 0px rgba(255,255,255,0.30), inset 0px 1px 2px 0px rgba(0,0,0,0.40);

}
```
[via](https://github.com/Getable/constructable/blob/9ab035a24731a2c551449aad099129288ae5c25e/client/pieces/button/index.css#L116-L130)

<div style="display: none">
Remove all instances of `.btn-primary` from the app, and the CSS will still come down. But, there's no way to know the css isn't needed anymore.
</div>




## Minification

```css
.checkout-jobsite-pane .delivery-edit-btn,
.checkout-jobsite-pane .contact-edit-btn {
  margin-bottom: 15px;
  float: right;
}
```
[via](https://github.com/Getable/constructable/blob/9ab035a24731a2c551449aad099129288ae5c25e/client/components/checkout-jobsite-step/index.css)

<div style="display: none">
ooof… that's a lot for the browser to download. It would be better if something could hash that classname and shorten it for us.
</div>





## Non-deterministic Resolution

What's the background color of the `<button>`?

```css
/* button-call.css */

.button-call {
  background: white;
}
```

```css
/* button.css */

.button {
  background: black;
}
```

```html
<button class="button-call button" />
```

<div style="display: none">
Answer: We don't know!

In CSS, the last file was loaded to win. If you're loading CSS async, or can't determine the order which your css files are bundled together, you can't predict what applying both these classes will do.

This is where we start to have specificity wars, and that's a never ending struggle once you start it.

Imagine you're on a page that loads just `button-call.css`, then you go to another page that loads `button.css`. Your same button is going to change background colors, and good luck trying to repro the issue!
</div>










## Breaking Isolation: Mistakes

What's the bug?

```html
<!-- button component -->
<button class="button"><span>{buttonText}</span></button>
```

```css
/* button.css */

.button {
  background: black;
  color: white;
}
```

```css
/* alert-danger.css */

.button > span {
  color: red;
}
```

<div style="display: none">
Answer: all buttons now have a red color.

This works because your new rule in `alert-danger.css` is more specific than the one on `button.css`. But, you've just accidentally changed the text color of all buttons!
</div>















## Breaking Isolation: Harder to Maintain
Alternatively, let's say you do this, what's the problem?

```css
/* button.css */

.button {
  background: black;
  color: white;
}
```

```css
/* alert-danger.css */

.alert-danger .button > span {
  color: red;
}
```

<div style="display: none">
Now, you've locked alert-danger into the dom structure of the button component. It makes it very hard to ensure you can change the button component without affecting who-knows-what-else.

### problems
* The DOM structure of button now can't be changed w/o breaking alert-danger
* There's now a specificity war between `alert-danger` and `button`. You aren't guaranteed that CSS changes to `button` won't break `alert-danger`.

</div>










## Breaking Isolation: The cascade gets in the way

```css
ul li button {

}
```

<div style="display: none">
Selectors are not very specific. The cascade can get in your way and cause unintentional side effects. The easy answer is to _always_ use class names. _Never_ style elements directly.
</div>









# So, Inline Styles?

<div style="display: none">
These problems were shamelessly stolen from the same deck that introduced [inline styles for react](https://speakerdeck.com/vjeux/react-css-in-js).

But, inline styles [have their own problems](https://byjoeybaker.com/react-inline-styles).
</div>

## The Myths of inline styles
* inline styles mean you can just learn JavaScript and won't need to learn CSS: Bullshit. Inline styles are still CSS. Not to mention there are things like media queries and transitions that CSS just does better than JS.
* CSS is a pain: Possibly true, but tools like LESS, SCSS, PostCSS, and Autoprefixer solve a lot of problems.

## No Style Fallbacks

There's no way to override a style on the same selector

You can do this in CSS:
```css
div {
  display: -webkit-flex;
  display: flex;
}
```

But not in HTML: This only takes the last `display` decoration.
```html
<div style="display: -webkit-flex; display: flex"></div>
```

## No Media Queries or Element Queries
* You have to use JS to detect widths and update inline styles accordingly.
* I was initially excited by this, because it means that element queries could be the default! But, if you go down that route, you'll have to use `.offsetWidth` and `window.resize` all over the place. That's crap for performance
* It also will cause a flash of unstyled content if you're server-rendering.

## No Cascading Styles
So, yes, isolation is good. But sometimes, a global state is nice. e.g.

* typography defaults or resets (normalize.css)
* `@keyframes`

## Performance
* [inline styles are slower than a stylesheet](http://jsperf.com/class-vs-inline-styles/6)
* it's a lot more content to download (gzip does help)



