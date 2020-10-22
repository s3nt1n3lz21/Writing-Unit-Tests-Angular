# Writing-Unit-Tests-Angular
How To Write Unit Tests For Angular Jasmine

# UI Coding Standards & Guidelines

# Contents
----
- [Setting Up ESLint In Visual Studio Code](#Setting-Up-ESLint-In-Visual-Studio-Code)
- [ESLint rules](#ESLint-rules)
- [Variable Naming Conventions](#Variable-Naming-Conventions)
- [Filename Naming conventions](#Filename-Naming-Conventions)
- [General Good Practises](#General-Good-Practises)
- [Writing Unit Tests](#Writing-Unit-Tests)
  - [Helpful Tips](#Helpful-Tips)
  - [HTML Tests](#HTML-Tests)
  - [TS Tests](#TS-Tests)
  - [Writing Integration Tests](#Writing-Integration-Tests)

# Setting Up ESLint In Visual Studio Code
---
We use ESLint to automatically check the syntax, formatting and conventions of our code. When you run `ng lint` it will go through all the syntax rules in `.eslintrc.js` and check our code against them. However you can set up ESLint to hightlight any potential issues within the editor as you type your code.

Within Visual Studio Code, install the ESLint extension and make sure to remove the TSLint extension. You can do this only for this project, so you don't affect the installed extensions for other projects/repos. 

You can also set up Visual Studio to automatically reformat and indent your code to align with the ESLint rules when you save a file. Go to File->Preferences->Settings. In the search bar, type 'save' and check the Format On Save box. You can also decide whether you want to format the file on save or the whole project.

![image.png](/.attachments/image-da28d52d-241b-4ea9-baa3-df6ffb551ab8.png)

Then go to the settings for the ESLint extension and Enable ESLint as a formatter.

![image.png](/.attachments/image-5bbece38-88be-432e-ba98-2d05ae7e5963.png)

# ESLint rules
---
This is a list of the current ESLint rules being implemented in Dashboard. Please update this whenever the linting rules are changed.

- No unused imports
- Imports should be ordered and similar imports are grouped together
- CamelCase naming convention
- Leading underscore for private variables
- Component names must end in Component
- Component selectors should be kebab-case
- Component selectors should start with 'app'
- Directive names must end in Directive
- Directive selectors should be camelCase
- Directive selectors should start with 'app'
- Not allowed to use the host metadata property
- Inputs cannot be renamed
- Not allowed to use the inputs metadata property
- Output names must not start with 'on'
- Outputs cannot be renamed
- Not allowed to use the outputs metadata property
- Should implement lifecycle interface methods
- Pipes should implement the PipeTransform interface
- Use interfaces when defining types
- Don't have to use dot notation
- Don't have to define accessibility modifier for fields and methods e.g. Public/Private
- Indentation must be 4 spaces
- Class members must be ordered
  - Static Fields
  - Instance Fields
  - Static Methods
  - Instance Methods
- Can have empty functions
- No empty interfaces
- No type definitions for numbers, booleans and strings except function parameters
- No constructors for interfaces and no methods named new in classes
- No non null assertions
- Define variables before use
- Use function types instead of interface or object type literals
- Use a space between variable names and types
- Use a space between methods names and return types
- No two function overloads that can be unified
- Extended classes must call super in constructor
- Always wrap code blocks in braces
- Newline not required at end of file
- Use === and !==
- For in loops should include an if statement
- No blacklisted field, method and variable names
- No regex pattern naming convention required for field, method and variable names
- No deprecated functions
- No maximum line length
- No bitwise operators
- No arguments.caller or arguments.callee
- No console methods except
  - log
  - warn
  - dir
  - timelog
  - assert
  - clear
  - count
  - countReset
  - group
  - groupEnd
  - table
  - dirxml
  - error
  - groupCollapsed
  - Console
  - profile
  - profileEnd
  - timeStamp
  - context
- No debugger statement
- Allow empty code block statements
- Disallow use of eval() function
- No fallthrough of case statements
- No primitive wrappers Number(), String(), Boolean()
- Disallow following imports
  - rxjs/Rx
- No throwing of literals as exceptions e.g. throw "error"
- No initialising of variables to undefined
- Disallow unused labels
- Disallow use of var
- Disallow leaving radix empty in parseInt()

# Variable Naming Conventions

---
- Avoid single-letter names. Be descriptive with your naming
- Use camelCase
- Private fields should be prefixed with an underscore
- Imported services should be prefixed with an underscore
- Subscriptions should be prefixed with a dollar sign and end with Subscription
- Subjects should be prefixed with A dollar sign and end with Subject

# Filename Naming Conventions:
---
- All components and services should be the lower case with ```-``` for spaces
- ```<filename>.<type>.ts```
  - E.g ```user-profile.component.ts```, ```user-profile.service.ts```

# General Good Practises
---
- No commented out code should be committed to master
- Use ```===``` and ```!==``` over ```==``` and ```!=```
- Use ```const``` when variables are not re-assigned and ```let``` when they are
- Group all consts and then group all lets
- Always set variables types 
- There should be no unused imports and variables
- Imports should be grouped by Angular, external, components and services
- There should be no unused services in the constructor
- Only quote properties that are invalid identifiers
- Use single quotes for strings
- Use shortcuts for booleans, but explicit comparisons for strings and numbers
- Use soft tabs (space character) set to 4 spaces
- Do not use multiple blank lines to pad your code
- Do not add spaces inside parentheses
- Do not add spaces inside brackets
- Add spaces inside curly braces
- Don’t save references to this. Use arrow functions or ```bind()``` function
- Start all comments with a space to make it easier to read
- Add unique IDs to all HTML elements that the testing team will require for locating elements, i.e. text fields, dropdown lists, checkboxes etc.

# Writing Unit Tests
---

In short, you should test everything that could possibly change or cause some event or function to be called. We do not test every static element and every single class that is applied to each element. But we do test that the correct child components we created are present and any classes that vary i.e ```[ngClass]```.

We use ```testing-utils.ts``` to provide a reusable ```TestBed``` where we import all the shared modules and mock all the services. We then import this ```TestBed``` into the unit test spec file for each component and add what we need to it. This ensures we only use mocked services in each component and that we don’t have to redefine these mocked services in every component.

For each unit test we then only have to add the declaration for the component we are testing and declarations for any mocked child components. We shouldn’t have to redefine the mocked services here. If we need to change what a function of a service returns for a particular unit test we can use a jasmine spy and change the return value temporarily.

```js
spyOn(httpClient, 'get').and.returnValue(throwError(error));
```

Avoid using ```toBeTruthy()``` and ```toBeFalsy()```, only use these when checking a component exists or checking a function that returns a ```boolean```. Don’t use them when checking inputs and fields as ```isLoading``` which should be a ```boolean``` could be the number 5 and the test will pass.

When testing inputs to child components grab the component using the componentInstance
``` js
const projectStatusSelectorComponent = componentFixture.debugElement.query(By.css('#sltShowProjectTypeSolutionDashboard')).componentInstance;
```

``` js
const hierarchySelectorComponent = componentFixture.debugElement.query(By.directive(HierarchySelectorMockComponent)).componentInstance;
```
    
# Helpful Tips
---

You can run the unit tests for just one file by replacing `describe` with `fdescribe` to focus only on those unit tests. Similarly, you can run an individual unit test by replacing `it` with `fit`. But make sure to remove these again when you are done.

If you go to ```localhost:9876``` you can load up a UI that displays the components you are testing and the results.

# HTML Tests
---

- You should test that key components are present and displayed in the UI
- You should test that any important text is displaying correctly in the UI.
- You should test every ```*ngIf``` and that components are displaying or not displaying depending on the value of variables
- You should test every ```*ngFor``` and that components are displaying the correct number of elements.
- You should test ```[ngClass]``` and that the right classes are applied depending on the value of variables.
- You should test child components call the correct function when their output event is emitted.
- You should test child components are passed the correct inputs.

Make sure to grab the element through the ```debugElement``` instead of the ```nativeElement``` e.g. ```componentFixture.debugElement.query(By.css(‘#anId’)).componentInstance```

# TS Tests
---

You do not have to test the inner workings of a function just that different inputs result in the right functions being called and the right fields being changed. If private fields are changed you can use key object notation to retrieve it still instead of dot notation. ```component[‘privateField’]``` instead of ```component.privateField```.

- You should test fields are initialized to the correct values.
- For each function you should test enough inputs that every part of function is executed.
- For each function you should test that other functions are called depending on its inputs.
- For each function you should test that fields are changed to the correct values depending on its inputs.

# Writing Integration Tests
---

In unit tests we replace all the child components and services with simple mocked versions. In Integration tests we include the actual child components. We write integration tests in a separate file. There are automated integration tests written by testers so developers shouldn’t have to write many of these.

There are many different integration tests we could create. We should make sure to agree on what to write integration tests for before writing any. We usually only test the most important and complex components that may have a lot of child components or experience bugs in the past.
