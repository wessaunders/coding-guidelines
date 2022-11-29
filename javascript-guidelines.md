# Javascript Coding Guidelines

This is intended to establish coding and styling guidelines for javascript.

The intention is that all code in any code base should look like a single person typed it, no matter how many people contributed.

# Table of contents
- [Basic Principles](#basic-principles)
- [Formatting](#formatting)
- [Variables, references, and declarations](#variables,-references,-and-declarations)

# Basic principles
- In general, if there is a situation that this document does not cover, refer back to the following set of basic principles that apply to all situations, regardless of context:
    - The Principle of Least Surprise (or Astonishment): you should choose a solution that everyone can understand, and that keeps them on the right track.
    - Keep It Simple Stupid (a.k.a. KISS): the simplest solution is more than sufficient.
    - You Ain’t Gonna Need It (a.k.a. YAGNI): create a solution for the problem at hand, not for the ones you think may happen later on. Can you predict the future?
    - Don’t Repeat Yourself (a.k.a. DRY): avoid duplication within a component, a source control repository or a bounded context, without forgetting the Rule of Three heuristic.
- In general, generated code should not need to comply with coding guidelines. However, if it is possible to modify the templates used for generation, try to make them generate code that complies as much as possible.
- Regardless of the elegance of someone’s solution, if it’s too complex for the ordinary developer, exposes unusual behavior, or tries to solve many possible future issues, it is very likely the wrong solution and needs redesign. The worst response a developer can give you to these principles is: “But it works?”.

# Formatting
- Tabs for Indentation
    - Use tabs for indenting your code. With tabs users can choose their desired width. This has positive implications for accessibility and screenreaders, putting people in control of how they want to view the code.

- No trailing whitespace
    - Always clean up any trailing whitespace in your .js files before committing.

- Chained Calls
    - Use indentation when making long method chains (more than 2 method chains). Use a leading dot, which emphasizes that the line is a method call, not a new statement.
    ```
    // bad 👎
    $('#items').find('.selected').highlight().end().find('.open').updateCount();

    // bad 👎
    $('#items').
    find('.selected').
      highlight().
      end().
    find('.open').
      updateCount();

    // good 👍
    $('#items')
    .find('.selected')
      .highlight()
      .end()
    .find('.open')
      .updateCount();

    // bad 👎
    const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
    .attr('width', (radius + margin) * 2).append('svg:g')
    .attr('transform', `translate(${radius + margin},${radius + margin})`)
    .call(tron.led);

    // good 👍
    const leds = stage.selectAll('.led')
    .data(data)
    .enter().append('svg:svg')
    .classed('led', true)
    .attr('width', (radius + margin) * 2)
    .append('svg:g')
    .attr('transform', `translate(${radius + margin},${radius + margin})`)
    .call(tron.led);

    // good 👍
    const leds = stage.selectAll('.led').data(data);
    ```
  
# Variables, references, and declarations
- Avoid using *var* - use *const* for all of your references
    > IMPORTANCE: This ensures that you can't reassign your references, which can lead to bugs and difficult to comprehend code.
    ```
    // bad 👎
    var a = 1;
    var b = 2;

    // good 👍
    const a = 1;
    const b = 2;
    ```
- If you must reassign references, use 'let' instead of 'var'.
    > IMPORTANCE: let is block-scoped rather than function-scoped like var.
    ```
    // bad 👎
    var count = 1;
    if (true) {
    count += 1;
    }

    // good 👍 , use the let.
    let count = 1;
    if (true) {
    count += 1;
    }
    ```
