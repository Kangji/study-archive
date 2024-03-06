# Clean Code

- Clean code enhances productivity

## Features of Clean Code

- **Readability**
- **Simplicity**
- **Self-documenting** - code itself explains its purpose
- **Consistency** - consistent style
- **Testability** - easily testable design
- **Modularity** - decomposing complex systems into simpler modules

## Clean Code Principle

- **Clear names**
    - name explains purpose
    - no too much abbreviation
    - naming convention
- **Unified word for single concept**
    - Multiple words make confusion
- **Smaller functions**
- **Minimize side effect**
    - When changing the value outside its scope
- **Don’t Repeat Yourself (DRY)**
- **Clear comments**
    - Self-explanatory code
    - Provide high-level abstraction

## Code Smell

- Indicators of **potential problem**
- Weakness in design - affects **performance, maintenance, scalability**
- Remove by **refactoring**

## Coding Style

- A set of guidelines and conventions to follow
- **First step to write clean code**
- Ensures that code is **consistent, readable, and maintanable**
- Consistency is important

### Adopting and Enforcing Coding Styles

- document (**style guide**)
- use tools (**linter**)
- **Code Review**

### Google’s Java Style Guide

- File name = Class name
- Unicode-escape non-ascii characters
- Source FIle Structure
    - Licence
    - Package
    - Static Import
    - Non-static Import
    - Class(or Interface)
        - Fields
        - Constructors
        - Methods
- Single blank line separation
- Line-wrapping if too long
- One variable at once
- Comments
    - Line comments - simple
    - Javadoc - API docs
- Correct order of modifiers
- Others
    - Do not omit `@Override`
    - Static methods via class
    - Don’t ignore exception
- Javadoc
    - `@param`, `@return`, `@throw`
    - For class: responsibility & major use case
