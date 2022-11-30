# CSS Coding Guidelines

This is intended to establish coding and styling guidelines for CSS.

# Table of contents
1. [Terminology](#terminology)
	- [Rule declaration](#rule-declaration)
	- [Selectors](#selectors)
	- [Properties](#properties)
2. [CSS](#css)
	- [Comments](#comments)
	- [ID Selectors](#id-selectors)
	- [Design patterns](#design-patterns)
3. [Formatting](#formatting)
	- [Use flexible/relative units](#use-flexible/relative-units)
	- [Use em for font-size property value](#use-em-for-font-size-property-value)
	- [!important](#!important)
	- [Double quotes around values](#double-quotes-around-values)
	- [Longhand versus shorthand rules](#longhand-versus-shorthand-rules)
	- [Mobile first media queries](#Mobile-first-media-queries)
	- [Do not use ID selectors](#do-not-use-id-selectors)
	- [Put blank lines between rule declarations](#put-blank-lines-between-rule-declarations)
	- [Put each selector on its own line when using multiple selectors in a rule declaration](#put-each-selector-on-its-own-line-when-using-multiple-selectors-in-a-rule-declaration)
	- [Space after function parameters](#space-after-function-parameters)
	- [Syntax style](#syntax-style)

# Terminology

## Rule declaration
A “rule declaration” is the name given to a selector (or a group of selectors) with an accompanying group of properties. Here's an example:
```css
.listing {
    font-size: 18px;
    line-height: 1.2;
}
```

## Selectors
In a rule declaration, “selectors” are the bits that determine which elements in the DOM tree will be styled by the defined properties. Selectors can match HTML elements, as well as an element's class, ID, or any of its attributes. Here are some examples of selectors:
```css
.my-element-class {
    /* ... */
}

[aria-hidden] {
    /* ... */
}
```
  
When using multiple selectors, give each selector its own line  
### Bad
```css
.container > img, .container > div, .container > section {
  background-color: #CCCCCC;
}
```

### Good
```css
.container > img,
.container > div,
.container > section {
  background-color: #CCCCCC;
}
```

## Properties
Finally, properties are what give the selected elements of a rule declaration their style. Properties are key-value pairs, and a rule declaration can contain one or more property declarations. Property declarations look like this:
```css
/* some selector */ {
    color: #333;
    background: #f1f1f1;
}
```

# CSS

## Comments
- Prefer line comments to block comments.
- Prefer comments on their own line. Avoid end-of-line comments.
- Write detailed comments for code that isn't self-documenting:
	- Uses of z-index
	- Compatibility or browser-specific hacks

## ID Selectors
While it is possible to select elements by ID in CSS, it should generally be considered an anti-pattern. ID selectors introduce an unnecessarily high level of [specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) to your rule declarations, and they are not reusable.

For more on this subject, read [CSS Wizardry's](https://csswizardry.com/2014/07/hacks-for-dealing-with-specificity/) article on dealing with specificity.

## Design Patterns

### The parent constrains the child
Leaf components shouldn't constrain width or height (unless it makes sense). That said, most components should default to fill the parent:

**```lib/components/Input/index.js```**
```js
import style from './style.css'

const Input = ({ children }) =>
  <input className={style.input}>
    {children}
  </input>

export default Input
```

**```lib/components/Input/style.css```**
```css
.input {
  box-sizing: border-box;
  padding: 10px;
  width: 100%;
}
```

### The parent doesn't assume the child's structure
Sometimes we don't want to fill the whole width by default. An example is the button component, which we want to resize itself based on title width.

In this cases, we should allow the parent component to inject styles into the child component's container. The child is responsible for choosing where parent styles are injected.

**```lib/components/Button/index.js```**
```js
import classNames from 'classnames'
import style from './style.css'

const Button = ({ children, className }) =>
  <button className={classNames(style.button, className)}>
    {children}
  </button>

export default Button
```

**```lib/components/Button/style.css```**
```css
.button {
  box-sizing: border-box;
  padding: 10px;
  width: 100%;
}
```

### Components never leak margin
All components are self-contained and their final size should never suffer margin leakage! This allows the components to be much more reusable!

### Bad
```
|--|-content size-|--| margin
 ____________________
|   ______________   | | margin
|  |              |  |
|  |              |  |
|  |              |  |
|  |______________|  |
|____________________| | margin

|---container size---|
```

### Good
```

   |-content size-|
    ______________
   |              |
   |              |
   |              |
   |______________|



```

### The parent spaces the children
When building lists or grids:

- Build list/grid items as separate components
- Use the the list/grid container to space children
- To space them horizontally, use margin-left
- To space them vertically, use margin-top
- Select the first-child to reset margins

**```lib/containers/Reviews/index.js```**
```css
import style from './style.css'

const Reviews = ({ items }) =>
  <div className={style.container}>
    {items.map(item =>
      <img src={item.image} alt={item.title} />
    )}
  </div>

export default Reviews
```

**```lib/containers/Reviews/style.css```**
```css
.container > img {
  margin-left: 10px;
}

.container > img:first-child {
  margin-left: unset;
}
```

### Variables, lots of variables!
Always define CSS variables to increase reuse and make styles more consistent. The CSS4 specification defines a way to declare native variables. We adopted them as the standard.

To define a variable accessible globally:
**```app/App/variables.css```**
```css
:root {
  --color-green-1: #6CCFAE;
  --color-green-2: #6B66B5;
  --color-green-3: #AAC257;
  --color-green-4: #68B5C1;
}
```

**```app/components/Button/styles.css```**
```css
.container {
  background-color:
    linear-gradient(
      0deg,
      var(--color-green-1),
      var(--color-green-2)
    );
}
```

# Formatting

## Use flexible/relative units
For maximum flexibility over the widest possible range of devices, it is a good idea to size containers, padding, etc. using relative units like ems or percentages and viewport units if you want them to vary depending on viewport width. Mozilla has a great article about responsive design [here](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Responsive/responsive_design_building_blocks#fluid_grids) that goes into more detail about this.

## Use em for font-size property value
Use em for font-size as opposed to px to ensure that font scaling works as expected across devices.

## !important
!important is the last resort that is generally used only when you need to override something and there is no other way to do it. Using !important is a bad practice and you should avoid it wherever possible.

### Bad
```css
.bad-code {
  font-size: 4rem !important;
}
```

## Double quotes around values
Where quotes can or should be included, use them, and use double quotes. For example:
```css
[data-vegetable="liquid"] {
  background-color: goldenrod;
  background-image: url("../../media/examples/lizard.png");
}
```

## Longhand versus shorthand rules
It is clearer and more obvious to use longhand properties, rather than terse shorthand.
- It is often harder to understand what the shorthand is doing. In the example below, it takes a while to pick apart exactly what the [font](https://developer.mozilla.org/en-US/docs/Web/CSS/font) syntax is doing.
```css
font: small-caps bold 2rem/1.5 sans-serif;
```

Whereas the following style is clearer:
```css
font-variant: small-caps;
font-weight: bold;
font-size: 2rem;
line-height: 1.5;
font-family: sans-serif;
```

- CSS shorthand comes with potential added pitfalls — default values are set for parts of the syntax that you don't explicitly set, which can produce unexpected resets of values you've set earlier in the cascade or other expected effects. The [grid](https://developer.mozilla.org/en-US/docs/Web/CSS/grid) property, for example, sets all of the following default values for items that are not specified:
	- grid-template-rows: none
	- grid-template-columns: none
	- grid-template-areas: none
	- grid-auto-rows: auto
	- grid-auto-columns: auto
	- grid-auto-flow: row
	- column-gap: 0
	- row-gap: 0
	- column-gap: normal
	- row-gap: normal
- Some shorthands only work as expected if you include the different value components in a certain order. This is the case in CSS animations. In the example below, the expected order is written as a comment:
```css
/* duration | timing-function | delay | iteration-count
  direction | fill-mode | play-state | name */
animation: 3s ease-in 1s 2 reverse both paused slidein;
```

In this example, the first value that can be parsed as a [<time>](https://developer.mozilla.org/en-US/docs/Web/CSS/time) is assigned to the [animation-duration](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-duration) property, and the second value that can be parsed as time is assigned to [animation-delay](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-delay). (For more information, see [animation](https://developer.mozilla.org/en-US/docs/Web/CSS/animation#syntax) syntax details.)
	
## Mobile first media queries
	
In a stylesheet that contains [media query](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries) styles for different target viewport sizes, first include the narrow screen/mobile styling before any other media queries are encountered. Add styling for wider viewport sizes via successive media queries. Following this rule has many advantages that are explained in Mozilla's [Mobile First](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Responsive/Mobile_first) article.
```css
/* Default CSS layout for narrow screens */

@media (min-width: 480px) {
  /* CSS for medium width screens */
}

@media (min-width: 800px) {
  /* CSS for wide screens */
}

@media (min-width: 1100px) {
  /* CSS for really wide screens */
}
```

## Do not use ID selectors
- they are less flexible; you can't add more if you discover you need more than one.
- they are harder to override because they have higher specificity than classes.
	
### Bad
```css
#editorial-summary {
  /* ... */
}
```	
	
### Good
```css
.editorial-summary {
  /* ... */
}
```
	
## Put blank lines between rule declarations

### Bad
```css
.avatar {
    border: 2px solid white;
    border-radius: 50%;
}

.no,
.nope,
.not_good {
    // ...
}

#lol-no {
  // ...
}
```

### Good
```css
.avatar {
    border: 2px solid white;
    border-radius: 50%;
}

.one,
.selector,
.per-line {
  // ...
}
```
	
## Put each selector on its own line when using multiple selectors in a rule declaration
When a rule has multiple selectors, put each selector on a new line. This makes the selector list easier to read and can help to make code lines shorter.

### Bad
```css
h1, h2, h3 {
  font-family: sans-serif;
  text-align: center;
}
```
	
### Good
```css
h1,
h2,
h3 {
  font-family: sans-serif;
  text-align: center;
}
	
```
	
## Space after function parameters
Function parameters should have spaces after their separating commas, but not before
```css
color: rgb(255, 0, 0);
background-image: linear-gradient(to bottom, red, black);
```
	
## Syntax style
- Use tabs for indentation.
- Prefer dashes over camelCasing in class names.
- Selector/opening brace, close brace, and each declaration on a separate line
- Include a space between the selector(s) and the opening curly brace.
- In properties, put a space after the separating colon (:) character, but not before.
- Always include a semicolon at the end of the last declaration, even though it isn't strictly necessary.
- Put closing braces } of rule declarations on a new line.

### Bad
```css
p { color: white; background-color: black; padding: 1rem; }
```
	
### Good
```css
p {
  color: white;
  background-color: black;
  padding: 1em;
}
	
custom-grid {
	background-color: white;
	color: black;
	height: 4em;
	padding: 1em;
	width: 3em;
}
```
