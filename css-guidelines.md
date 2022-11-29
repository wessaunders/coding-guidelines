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
	- Border

# Terminology

## Rule declaration
A “rule declaration” is the name given to a selector (or a group of selectors) with an accompanying group of properties. Here's an example:
```
.listing {
    font-size: 18px;
    line-height: 1.2;
}
```

## Selectors
In a rule declaration, “selectors” are the bits that determine which elements in the DOM tree will be styled by the defined properties. Selectors can match HTML elements, as well as an element's class, ID, or any of its attributes. Here are some examples of selectors:
```
.my-element-class {
    /* ... */
}

[aria-hidden] {
    /* ... */
}
```
  
When using multiple selectors, give each selector its own line  
### Bad
```
.container > img, .container > div, .container > section {
  background-color: #CCCCCC;
}
```

### Good
```
.container > img,
.container > div,
.container > section {
  background-color: #CCCCCC;
}
```

## Properties
Finally, properties are what give the selected elements of a rule declaration their style. Properties are key-value pairs, and a rule declaration can contain one or more property declarations. Property declarations look like this:
```
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
```
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
```
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
- Prefer line comments (// in Sass-land) to block comments.
- Prefer comments on their own line. Avoid end-of-line comments.
- Write detailed comments for code that isn't self-documenting:
	- Uses of z-index
	- Compatibility or browser-specific hacks
