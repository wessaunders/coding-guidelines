# Javascript Coding Guidelines

This is intended to establish coding and styling guidelines for javascript.

# Table of contents
- [Basic Principles](#basic-principles)
- [Variables, references, and declarations](#variables,-references,-and-declarations)

# Basic principles
- In general, if there is a situation that this document does not cover, refer back to the following set of basic principles that apply to all situations, regardless of context:
    - The Principle of Least Surprise (or Astonishment): you should choose a solution that everyone can understand, and that keeps them on the right track.
  - Keep It Simple Stupid (a.k.a. KISS): the simplest solution is more than sufficient.
  - You Ain’t Gonna Need It (a.k.a. YAGNI): create a solution for the problem at hand, not for the ones you think may happen later on. Can you predict the future?
  - Don’t Repeat Yourself (a.k.a. DRY): avoid duplication within a component, a source control repository or a bounded context, without forgetting the Rule of Three heuristic.
- In general, generated code should not need to comply with coding guidelines. However, if it is possible to modify the templates used for generation, try to make them generate code that complies as much as possible.
- Regardless of the elegance of someone’s solution, if it’s too complex for the ordinary developer, exposes unusual behavior, or tries to solve many possible future issues, it is very likely the wrong solution and needs redesign. The worst response a developer can give you to these principles is: “But it works?”.

# Variables, references, and declarations
- Use *const* for all of your references
  - avoid using *var*
  > IMPORTANCE: This ensures that you can't reassign your references, which can lead to bugs and difficult to comprehend code.
  ```
  // bad 👎
  var a = 1;
  var b = 2;

  // good 👍
  const a = 1;
  const b = 2;
  ```
- Use *let* instead of *var*
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
