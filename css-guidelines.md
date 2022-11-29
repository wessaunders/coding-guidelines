# CSS Coding Guidelines

This is intended to establish coding and styling guidelines for CSS.

# Table of contents
1. [Terminology](#terminology)
	- [Rule declaration](#rule-declaration)
	- [Selectors](#selectors)
	- [Properties](#properties)
2. [CSS](#css)
	- [Formatting](#formatting)
	- [Comments](#comments)
	- [ID Selectors](#id-selectors)
	- [Design patterns](#design-patterns)

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

**Use em for font-size: property value**
Use em for font-size as opposed to px to ensure that font scaling works as expected across devices.

# CSS

## Formatting
- Use tabs for indentation.
- Prefer dashes over camelCasing in class names.
- Underscores and PascalCasing are okay if you are using BEM (see OOCSS and BEM below).
- Do not use ID selectors.
- When using multiple selectors in a rule declaration, give each selector its own line.
- Put a space before the opening brace { in rule declarations.
- In properties, put a space after, but not before, the : character.
- Put closing braces } of rule declarations on a new line.
- Put blank lines between rule declarations.

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
```
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
```
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
```
:root {
  --color-green-1: #6CCFAE;
  --color-green-2: #6B66B5;
  --color-green-3: #AAC257;
  --color-green-4: #68B5C1;
}
```

**```app/components/Button/styles.css```**
```
.container {
  background-color:
    linear-gradient(
      0deg,
      var(--color-green-1),
      var(--color-green-2)
    );
}
```
