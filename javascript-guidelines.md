
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
