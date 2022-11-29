# CSS Coding Guidelines

This is intended to establish coding and styling guidelines for CSS.

# Table of contents
1. [Terminology](#terminology)
	- [Rule declaration](#rule-declaration)
	- [Selectors](#selectors)
	- [Properties](#properties)
2. [CSS](#css)
	- Formatting
	- Comments
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
When using multiple selectors, give each selector its own line

Organize one selector per line, even when placing all of them at the same line doesn't exceed 80 columns.
