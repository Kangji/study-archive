# Requirements Specification

## Types of Requirements

- **Functional**: Input-Output behavior
- **Non-functional**: Performance, privacy, reliability, etc.
- **Constraint**: Hardware resource, infra, etc.

## Requirements Engineering

- **Eliciting**: Gathering (and helping users to define) requirements
- **Analyzing**: Formularizing the requirements gathered
- **Documenting**: Formal or semiformal documentation

## Eliciting Strategies

- **Interview**
- Observation
- Workshop
- **Prototyping**

### Eliciting Challenges

- **Scope**: Maybe out of scope
- **Understanding**: Users do not know their requirements
- **Volatility**: Requirements may change

## Analyzing Techniques

- **User Story**
- **Use Case**
- Software Requirements Specification (SRS)

## User Story

- **Purpose** & **Value** of functional requirements
- **Who, What, Why** (role, capability, benefit)

### User Story Evaluation

- **Independent**: independent to others
- **Negotiable**
- **Valuable**: valuable to someone
- **Estimable**: cost must be estimable
- **Small**: cover single thing
- **Testable**: able to set **acceptance criteria**

## User Acceptance Criteria

- **Behavior-Driven Development (BDD)** approach
- **Given, When, Then** (pre-condition, event, outcome)

## Use Case

- **Interaction** between **actor** and **system**
- **Actor’s point of view**
- More detailed than user story
- To understand interaction sequence

### Creating Use Case

- Identify **Actor** & **Goal**
- **Main Success Scenario (steps)**
- List **Failure Extensions** (Failure cases & how to handle)
- List **Variants** of main scenario (branching)

## Wireframe

- Skeleton UIs and interaction
    - content hierarchy
    - space distribution
    - possible app user actions
    - app features
    - transition between app pages

## Specifications

- Explicit set of requirements **to be satisfied**
- **Documenting** requirements & system

### Good Specifications

- **Adequate**: properly state the requirements
- **Consistent**: no conflict
- **Complete**: implementation must be available
- **Unambiguous**
- **Minimal**: include only related requirements
- **Communicable**: readable

## Type of Specifications

- Informal: **natural language**
    - Ambiguity & Contradiction Issue
- Semi-formal: **graph / diagram**
- Formal: mathematics based

## Unified Modeling Language (UML)

- Standard of visualizing the design
- structural(static) & behavioral(dynamic)

### Class Diagram

- Class
- Attributes
- Methods
- Relationships
    - Association
    - Aggregation
    - Composition
    - Inheritance
    - Realization
    - Dependency

### Visibility

- +: public
- -: private
- #: protected
- ~: package (default)
