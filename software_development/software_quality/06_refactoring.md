# Refactoring

- Change internal structure without changing external behavior
- TDD required - **Test Suite**

## Benefits

- Enhance software design
- Improve readability
- Easier to find bugs
- Facilitate dev process

## When to Refactor

- Rules of three: similar code repeated three times
- Preparatory refactoring: do before adding new feature
- Comprehension refactoring: do while reading a code
- Litter-pickup refactoring: do later

## When NOT to Refactor

- External API / Blackbox
- When better to rewrite rather than refactor

## Refactoring Approaches

- Basic Methods
    - Extract / Inline method / variable
    - Split phase (of a method)
- Encapsulation
    - Extract class
    - Encapsulate variable / collection
    - Replace primitive with object
    - Replace parameter with query
    - Combine related functions into class
- Moving features
    - Move method / field
    - Split loop
    - Replace loop with pipeline (MapReduce)
- Organizing Data
    - Introduce parameter object
    - Split variable
    - Replace derived variable with query
    - Change reference to value
- Simplifying Condition
    - Decompose / Consolidate Condition
    - Use Guard Clause for Nested Condition
    - Replace condition with polymorphism
- Dealing with Inheritance
    - Pull Up/Push Down Field/Method
    - Extract Superclass
    - Collapse Hierarchy (Remove subclass that does nothing additional)