# React Coding Guidelines

This is intended to establish coding and styling guidelines for react.

# Table of contents
- [Basic rules](#basic-rules)
- [Project organization](#project-organization)
- [Class vs. functional components](#class-vs.-functional-components)
- [Component design](#component-design)
- [Maintainability](#maintainability)
- [Layout](#layout)
- [Formatting](#formatting)
- [Naming](#naming)
- [Declaration](#declaration)
- [Performance](#performance)
- [Redux](#redux)
- [Documentation](#documentation)
- [Testing](#testing)

# Basic principles
- In general, if there is a situation that this document does not cover, refer back to the following set of basic principles that apply to all situations, regardless of context:
    - The Principle of Least Surprise (or Astonishment): you should choose a solution that everyone can understand, and that keeps them on the right track.
  - Keep It Simple Stupid (a.k.a. KISS): the simplest solution is more than sufficient.
  - You Ain’t Gonna Need It (a.k.a. YAGNI): create a solution for the problem at hand, not for the ones you think may happen later on. Can you predict the future?
  - Don’t Repeat Yourself (a.k.a. DRY): avoid duplication within a component, a source control repository or a bounded context, without forgetting the Rule of Three heuristic.
- In general, generated code should not need to comply with coding guidelines. However, if it is possible to modify the templates used for generation, try to make them generate code that complies as much as possible.
- Regardless of the elegance of someone’s solution, if it’s too complex for the ordinary developer, exposes unusual behavior, or tries to solve many possible future issues, it is very likely the wrong solution and needs redesign. The worst response a developer can give you to these principles is: “But it works?”.

# Project organization
- Only include one React component per file.
- Always use JSX syntax.

- Project components should be separated into separate descriptive directories.
  - All components should be contained within a Components directory.
  - All constants should be contained within a Constants directory.
  - All redux functionality (services, reducers, actions) should be contained within separate directories within a Store directory.
```
|--src
    |--Components
    |--Constants
    |--Store
        |--Actions
        |--Reducers
        |--Services
```
 
- All components should always be defined as a directory, named with pascal casing. 
  - The main component file should be component name.js
  - Component styles should be located within a component name.module.css file in the same directory as the component’s JavaScript
```
Lightsaber
|--lightsaber.js
|--lightsaber.module.css
```

- Colocate unit tests
    - In modern React applications it is conventional to co-locate your JavaScript test files alongside the modules being tested.
    - Colocating tests can make it easier to see what files completely lack tests, and they simplify the import code within the test files as all imports are now relative to the module under test. Some teams find this structure also supports test-driven development. create-react-app and other bootstrapping tools assume co-located tests will be used, and modern test runners like Jest will automatically detect and run these *.test.js files.
```
Jedi/
    jedi.js
    jedi.module.css
    jedi.test.js
```

# Class vs. functional components
- Prefer functional components over class-based components
  - With the introduction of hooks to React, the core maintainers of React took a clear preference for the future of functional components over class components. Functional components had long been used for stateless components that did simple rendering, since they could be more readily instantiated and were more performant.
  - Functional components also get around many of the prevailing issues with using the “this” keyword in class components. A number of potential bug cases can be avoided by using classless components.
  - Functional components and hooks like useEffect(), useState(), useEffect(), and useContext() result in more concise and readable code compared to classes. 
    - Consider the following class component that displays data from the NASA API:
    ```
    class NasaData extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          data: [],
        };
      }
      componentDidMount() {
        fetch("https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY")
          .then((res) => res.json())
          .then((json) => {
            this.setState({
              data: json,
            });
         });
      }

      render() {
        const { data } = this.state;

        if (!data.length)
        return (
          <div>
            <h1> Fetching data.... </h1>{" "}
          </div>
        );

        return (
          <>
          <h1> Fetch data using Class component </h1>{" "}
          {data.map((item) => (
            <div key={item.id}>{item.title}</div>
          ))}
          </>
        );
      }
    }
    ```
      
    - The same component can be written using hooks:
    ```
    const NasaData = () => {
      const [data, setdata] = useState(null);

      useEffect(() => {
        fetch("https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY")
          .then((res) => res.json())
          .then((json) => {
            setdata(json);
          });
        }, [data]);

      return (
        <>
          <h1> Fetch data using Class component </h1>{" "}
          {data.map((item) => (
            <div key={item.id}>{item.title}</div>
          ))}
        </>
      );
    };
    ```
    - Even though the above block of code does the same thing as the class component, it is less complex, minimal, and easy to understand which contributes to a better developer experience.

- There are situations where class-based components may be necessary.  Keep in mind that as they grow class-based components start becoming complex and harder to understand. In addition to that, these components are less abstract. 
- If you are building a class-based component, prefer class extends React.Component over React.createClass. 
```
// bad
const Listing = React.createClass({
  // ...
  render() {
    return <div>{this.state.hello}</div>;
  }
});

// good
class Listing extends React.Component {
  // ...
  render() {
    return <div>{this.state.hello}</div>;
  }
}
```

# Component design
- A component should have a single purpose within the system it functions in. In general, a component either serves as a piece of the UI or is responsible for orchestrating the interaction between other components. It is never a combination of those. This rule is widely known as the Single Responsibility Principle, one of the S.O.L.I.D. principles.  
  - In case you notice that any component becomes huge and difficult to maintain, break it up into as many smaller components. For example, create a Button component that can handle icons. You should ensure that making it more modular to cover many cases with the same piece of code. Make sure that the components should be abstract enough but not overly complex.
    - Tip: A component with the word And in it is an obvious violation of this rule.
- Build opinionated components
  - Rather than exposing a prop for every kind of control, components should lean towards less flexibility and more opinionated designs. This may mean that certain elements of the component can’t be changed by the caller, but that can be a feature of this point, rather than a bug.
  - For example, a basic Label component may have an Icon and a Text child. When building this new component, it may be tempting to add a prop for everything from font color to font style (italic, bold, underline, etc), even to icon rotation (for reversing arrows, for example). This style of development may fit every use case, but it would also require significantly more testing, increase bundle sizes, and complexity. And that’s without considering future use-cases that were overlooked.
  - Rather than building in maximum flexibility, it will be easier to build, test, and later maintain components with a narrower functionality, fitting only the needs of uses on the primary roadmap - which also reinforces the single responsiblity of the component.
- Components should be responsive by default
  - One exception to the above rule about opinionated components is in their placement, sizing, and container styles.
  - It’s extremely useful to build components to be flexible by default. Sometimes, data needs to be displayed in a precise, true-to-size way, but usually that is not the case. With the nearly infinite possible combinations of screen sizes, it’s actually easier to build components that can be resized to almost any practical size. Example use cases should include cases of smaller and larger containers for components, to verify that they can be scaled up or down without breaking the design.
- Use useMemo() for calculations
  - Any time components perform complex calculations like sorting lists or averaging data, if this work is done inside the body of the component, there’s a risk of this work being done on every render, regardless of whether or not the data has changed. Use the “useMemo” hook to memoize these calculations.
- Use useCallback() for functions passed to children
  - Similar to how useMemo() is used, use the useCallback() function any time a function is instantiated to pass to children. This will prevent the function from getting recreated on every rerender.

# Maintainability
- Functions should not exceed 8 statements.
  - Break it down into multiple small and focused methods with self-explaining names, but make sure the high-level algorithm is still clear.
> Why? A method that requires more than 7 statements is simply doing too much or has too many responsibilities. It also requires the human mind to analyze the exact statements to understand what the code is doing. 
    
- Take Out Extraneous Comments From the Code
  - Only add comments where they are necessary to avoid confusion when modifying the code later.
    - This includes describing which developer changed which line of code and when.  This is unnecessary since it is already tracked by the version control system.
  - Also remove Console.log and other debugger statements.

- Don’t comment out code
  - Never check in code that is commented out. Instead, use a work item tracking system to keep track of some work to be done. Nobody knows what to do when they encounter a block of commented-out code. Was it temporarily disabled for testing purposes? Was it copied as an example? Should I delete it?

- Avoid conditions with double negatives
  - Although a property like customer.HasNoOrders makes sense, avoid using it in a negative condition like this:
  ```
  let hasOrders = !customer.HasNoOrders;
  ```
  - Double negatives are more difficult to grasp than simple expressions, and people tend to read over the double negative easily.
 
- Don’t use “magic” numbers
  - Don’t use literal values, either numeric or strings, in your code, other than to define symbolic constants. For example:
  ```
  // bad
  const userTimeout = 10000;
  
  // good
  const timeoutInMilliseconds = 10000;
  const userTimeout = timeoutInMilliseconds;
  ```

  - Strings intended for logging or tracing are exempt from this rule. Literals are allowed when their meaning is clear from the context, and not subject to future changes, For example:
  ```
  // okay
  const mean = (a + b) / 2;
  
  // clear enough  
  const waitMilliseconds = waitTimeInSeconds * 1000; 
  ```
  
  - If the value of one constant depends on the value of another, attempt to make this explicit in the code.
  ```
  const MaxItems = 32;
  const HighWaterMark = 3 * MaxItems / 4; 
  ```
  
- Don’t make explicit comparisons to *true* or *false*.
  - It is usually bad style to compare a bool-type expression to true or false. For example:
  ```
  // wrong; bad style
  while (condition == false) {}
  
  // also wrong
  while (condition != true) {}

  // where do you stop?
  while (((condition == true) == true) == true) {}
  
  // good
  while (condition) {}
  ```
  
- Use map() to dynamically render repeated HTML blocks. For example, you can use map() to render a list of items in \<li\> tags.
```
const Items = () => {
  const arr = ["item1", "item2", "item3", "item4", "item5"];
  
  return (
    <>
    {arr.map((elem, index) => {
      <li key={elem+index}>{elem}</li>;
    })}
    </>
  );
};  
```

- For comparison purposes, here is how you can render the list without map(). This approach is very repetitive.
```
const List = () => {
  return (
    <ul>
      <li>Item1</li>
      <li>Item2</li>
      <li>Item3</li>
      <li>Item4</li>
      <li>Item5</li>
    </ul>
  );
};
```

# Layout
- Follow this layout style for JSX syntax:
```
// bad
<Foo superLongParam="bar"
     anotherSuperLongParam="baz" />

// good
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
/>

// if props fit in one line then keep it on the same line
<Foo bar="bar" />

// children get indented normally
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
>
  <Quux />
</Foo>

// bad
{showButton &&
  <Button />
}

// bad
{
  showButton &&
    <Button />
}

// good
{showButton && (
  <Button />
)}

// good
{showButton && <Button />}
```

- Order component methods in the following order:
1. Hooks declarations
2. State assignments
3. Select assignments
4. Lifecycle events
5. All other functions in alphabetical order
6. Return
> Why?  Keeping the methods in order encourages consistency, and putting all the defined (other) functions in alphabetical order does the same.  It also makes it much more obvious if a function has accidentally been declared twice or has been repeated.
```
import React, { useEffect, useState } from 'react';
import { useDispatch, useSelector } from 'react-redux';

import { changeFooBar } from '../../Store/Services/FooBar';

const Foo = () => {
    const dispatch = useDispatch();

    const [fooText, setFooText] = useState('');
    const { bar } = useSelector(state => state.foobar);

    useEffect(() => {
        if (bar) {
            dispatch(changeFooBar(bar));
        }
    }, [bar]);

    onBarClick = () => {
       dispatch(changeFooBar(bar)); 
    }

    onFooTextChange = (e) => {
        setFooText(e.target.value);
    }

    return (
        <div>
            <input
                onChange={onFooTextChange}
                type='text'
                value={fooText}
            />
            <button
                onClick={onBarClick}>
                Click the foobar button!
            </button>
        </div>
    );
}
```

- Keep component props in alphabetical order when declaring the component
> Why?  Just like keeping the component functions in alphabetical order encourages consistency, keeping the props in alphabetical order encourages consistency as well.  It also makes it much more obvious if a prop has been accidentally repeated.
```
<Enterprise
    captain='James T Kirk'
    numberOfCrew=1500
    topSpeed='Warp 9'
/>
```

- Align and sort HTML properties

Fit it all on the same line if there is a single property. Otherwise, put each property on a line of its own, indented one tab, in sorted (alphabetical) order. The closing angle brace should be on a line of its own, indented the same as the opening angle brace. This makes it easy to see the props at a glance.

> Consistent property ordering makes it easier to manage complex components.

Properties should generally be sorted into the following groups, and ordered alphabetically within those groups:
- key should be first if required.
- Values
- Event handlers (on...)
```
// Bad

// property not on its own line
<div className="highlight"      
     key="highlight-div"
>

// closing brace not on its own line
<div                            
    key="highlight-div"
    className="highlight">
    
// key is not first   
<div                            
    className="highlight"
    key="highlight-div"
>

// handlers should be after values
<Image                          
    key="highlight-div"
    onClick={ () => this.onClick() }
    size="40"
/>

// Good
<div className="highlight">

<div
    key="highlight-div"
    className="highlight"
>

<Image
    key="highlight-div"
    className="highlight"
    onClick={ () => this.onClick() }
/>
```

# Formatting 
- Line length
  - Keep the length of each line under 130 characters.

- Tabs for Indentation
  - Use tabs for indenting your code. With tabs users can choose their desired width. This has positive implications for accessibility and screenreaders, putting people in control of how they want to view the code.

- Braces
  - Opening braces go on the same line, Your opening braces go on the same line as the statement.
  ```
  // bad 👎
  if (true)
  {
    console.log('losing');
  }

  // good 👍
  if (true) {
    console.log('winning');
  }
  ```

- Keyword Spacing
  - Place 1 space before the opening parenthesis in control statements (if, while etc.). Place no space between the argument list and the function name in function calls and declarations.
  ```
  // bad 👎
  if(isJedi) {
    fight ();
  }

  // good 👍
  if (isJedi) {
    fight();
  }

  // bad 👎
  function fight () {
    console.log ('Swooosh!');
  }

  // good 👍
  function fight() {
    console.log('Swooosh!');
  }  
  ```
  - Also, notice the use of whitespace before and after the condition statement. What if you want to write 'else' or 'else if' along with your 'if'...
  ```
  // bad 👎
  if (true)
  {
    console.log('losing');
  }
  else if (false)
  {
    console.log('this is bad');
  }
  else
  {
    console.log('not good');
  }

  // good 👍
  if (true) {
    console.log('winning');
  } else if (false) {
    console.log('this is good');
  } else {
    console.log('finally');
  } 
  ```
  
- Space Around Infix Operators
  - Add a space around operators like +, -, ==, etc.
  ```
  // bad 👎
  const x=y+5;

  // good 👍
  const x = y + 5;  
  ```

- Always use double quotes (") for JSX attributes, but single quotes (') for all other JS.
> Why? Regular HTML attributes also typically use double quotes instead of single, so JSX attributes mirror this convention.
```
// bad
<Foo bar='bar' />

// good
<Foo bar="bar" />

// bad
<Foo style={{ left: "20px" }} />

// good
<Foo style={{ left: '20px' }} />
```

- Always include a single space in your self-closing tag.
```
// bad
<Foo/>

// very bad
<Foo                 />

// bad
<Foo
 />

// good
<Foo />
```

- Do not pad JSX curly braces with spaces.
```
// bad
<Foo bar={ baz } />

// good
<Foo bar={baz} />
```
- Wrap JSX tags in parentheses when they span more than one line.
```
// bad
render() {
  return <MyComponent variant="long body" foo="bar">
           <MyChild />
         </MyComponent>;
}

// good
render() {
  return (
    <MyComponent variant="long body" foo="bar">
      <MyChild />
    </MyComponent>
  );
}

// good, when single line
render() {
  const body = <div>hello</div>;
  return <MyComponent>{body}</MyComponent>;
}
```

- Always self-close tags that have no children.
```
// bad
<Foo variant="stuff"></Foo>

// good
<Foo variant="stuff" />
```

- If your component has multiline properties, close its tag on a new line.
```
// bad
<Foo
  bar="bar"
  baz="baz" />

// good
<Foo
  bar="bar"
  baz="baz"
/>
```
  
# Naming
- Use US English
  - All identifiers (such as types, type members, parameters and variables) should be named using words from the American English language.
- Choose easily readable, preferably grammatically correct names. 
  - For example, *HorizontalAlignment* is more readable than *AlignmentHorizontal*.
  - Favor readability over brevity. The property name *CanScrollHorizontally* is better than *ScrollableX* (an obscure reference to the X-axis).
  - Avoid using names that conflict with keywords of widely used programming languages.

- Don’t include numbers in variables, parameters and type members
  - In most cases they are a lazy excuse for not defining a clear and intention-revealing name.
- Don’t prefix fields
  - For example, don’t use g_ or s_ to distinguish static from non-static fields. A method in which it is difficult to distinguish local variables from member fields is generally too big. 
  - Examples of incorrect identifier names are: *_currentUser*, *mUserName*, *m_loginTime*.
- Don’t use abbreviations
  - For example, use ButtonOnClick rather than BtnOnClick. Avoid single character variable names, such as i or q. Use index or query instead.
    ```
    // bad
    const BtnOnClick;

    // good
    const ButtonOnClick;

    // bad
    let i = 1;
    
    // good
    let index = 1;
    ``` 
  - Exceptions: Use well-known acronyms and abbreviations that are widely accepted or well-known in your work domain. For instance, use acronym *UI* instead of *UserInterface* and abbreviation *Id* instead of *Identity*.

- Name members, parameters and variables according to their meaning and not their type
  - Use functional names. For example, *GetLength* is a better name than *GetInt*.
  - Don’t use terms like Enum, Class or Struct in a name.
  - Identifiers that refer to a collection type should have plural names.

- **Folders/Directories**: Use PascalCase for folders/directories.
- **Filename**: Use PascalCase for filenames. 
  - For example: ReservationCard.js, Menu.js
- **Reference Naming**: Use PascalCase for React components and camelCase for their instances.  
```
// bad
import successButton from './SuccessButton';

// good
import SuccessButton from './SuccessButton';

// bad
const SuccessButton = <SuccessButton />;

// good
const successButton = <SuccessButton />;
```
- Use camelCase for functions declared inside React components.
  - Exmple: handleInput() or showElement()
- **Component Naming**: Use the filename as the component name. 
  - For example, ReservationCard.js should have a reference name of ReservationCard. 
- **Props Naming**: Avoid using DOM component prop names for different purposes.
> Why? People expect props like style and className to mean one specific thing. Varying this API for a subset of your app makes the code less readable and less maintainable, and may cause bugs.
```
// bad
<MyComponent style="fancy" />

// bad
<MyComponent className="fancy" />

// good
<MyComponent variant="fancy" />
```
- **Props Naming**: Always use camelCase for prop names.
```
// bad
<Foo
  UserName="hello"
  phone_number={12345678}
/>

// good
<Foo
  userName="hello"
  phoneNumber={12345678}
/>
```

- **Event handlers**: Name event handlers onEventName
    - This is consistent with React’s event naming: onClick, onDrag, onChange, etc.
    ```
    <Component 
        onClick={ e => this.onClick( e ) } 
        onLaunchMissiles={ num => this.onLaunchMissiles( num ) } 
    />    
    ```

# Declaration
- Do not use underscore prefix for internal methods of a React component.
> Why? Underscore prefixes are sometimes used as a convention in other languages to denote privacy. But, unlike those languages, there is no native support for privacy in JavaScript, everything is public. Regardless of your intentions, adding underscore prefixes to your properties does not actually make them private, and any property (underscore-prefixed or not) should be treated as being public.
```
// bad
React.createClass({
  _onClickSubmit() {
    // do stuff
  },

  // other stuff
});

// good
class extends React.Component {
  onClickSubmit() {
    // do stuff
  }

  // other stuff
}
```

# Performance
- Avoid Using State (if possible)
  - React state keeps track of the data which when changed triggers the React component to re-render. When building React applications, avoid using state as much as possible since the more state you use, the more data you have to keep track of across your app.
  - One way of minimizing the use of state is by declaring it only when necessary. For instance, if you are fetching user data from an API, store the whole user object in the state instead of storing the individual properties.
  - Instead of doing this:
    ```
    const [username, setusername] = useState('')
    const [password, setpassword] = useState('')
    ```

  - Do this:
    ```
    const [user, setuser] = useState({})
    ```

# Redux
- All component access to redux functionality should be through functions defined in services instead of calling reducers or actions directly.
> Why?  This is for consistency.  While it is certainly possible to access reducers and actions directly from a component it can become more confusing  when debugging or modifying the component later.

- Prefer using fetch() over a third-party library such as axios when making external calls to APIs or other web utilities
> Why?  Fetch is part of the HTML5 standard whereas other libraries may or may not be maintained or available in the future.

# Documentation
- **JSDoc**

- Avoid inline comments
  - If you feel the need to explain a block of code using a comment, consider replacing that block with a method with a clear name.
- Only write comments to explain complex algorithms or decisions
  - Try to focus comments on the why and what of a code block and not the how. Avoid explaining the statements in words, but instead help the reader understand why you chose a certain solution or algorithm and what you are trying to achieve. If applicable, also mention that you chose an alternative solution because you ran into a problem with the obvious solution.
- Don’t use comments for tracking work to be done later
  - Annotating a block of code or some work to be done using a TODO or similar comment may seem a reasonable way of tracking work-to-be-done. But in reality, nobody really searches for comments like that. Use a work item tracking system to keep track of leftovers.
- Don't use comments to track work that was already completed
  - Adding a comment that describes when the work was completed (and by whom) may seem reasonable, but like the TODO comments, nobody really searches for comments like that either.  Also the source control system will already keep track of when the changes were made and by whom, so these type of comments are redundant.


# Testing
- One of the most common testing frameworks for React is Jest, and it provides an environment where you can execute your tests.
- Jasmine?
- Testing guarantees that the parts behave as you would anticipate and reduces challenges for React Developers.

- Unit test components
  - Write Tests for Each React Component
  - Components should tested as closely as possible to how they will be used. For this reason, the react-testing-library is an excellent library to use when unit testing components. It provides utilities to find and interact with components in rendered DOM. The library has many fantastic guides on their website.
  - As part of unit testing components, when possible, mock out complex rendered children. This will make tests more performant, less fragile, and easier to debug.
- Unit test complex internal logic
  - Components often have complicated logic that controls how they work. One great example of this is a form component’s validation logic.
  - Rather than trying to test this logic by interacting directly with the component, extract it out into a utility function (either in the same file as a named export or a separate utility file) and test it separately.  

- Writing tests for all code
  - 
  - When it comes to the implementation of any programming language, adequate testing is needed. The goal is to ensure that the new code added to your project integrates with the existing code. There are chances of breaking existing functionality. Writing tests for all codes can eradicate the issue.
  - Create a _Test_ directory within the directory to conduct relevant tests.
  - Divide tests in React into testing the functionality of components and tests on the application once it renders in the browser. Use cross-browser testing tools that can help with the objective of testing the latter category. For the former, use a JavaScript test runner, Jest, to help to emulate the HTML DOM using jsdom to test React components.
  - A completely accurate test is only possible in a browser that has been long associated with the real device. Also, it can be said that Jest provides a good approximation of the real testing environment that can assist a developer a lot in the development phase of the project. Also, this principle serves as React practical advice for improving code quality.


