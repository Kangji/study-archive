# Software Development Life Cycle

## 6 Phase

1. **Requirements** Specification
    - Market research
    - Product definition
2. System Design
    - **Specification** of system(architecture, data structure, etc.)
    - Rigorous plan
3. Implementation
4. Testing
5. Deployment
6. Maintenance

## Software Process Model

* Abstract representation of development process
* **What, when, how** to do in each phase

## Waterfall Model

* Rigid sequential process of SDLC

### Pros

* Easy to follow / estimate cost
* Strong control / documentation
* Scalable to large projects

### Cons

* Requires **perfect planning**
* **No flexible response** to change

## Waterfall Extension: Iterative Model

* Each **iteration** includes whole SDLC
* **Feedback** at the end of iteration

## Waterfall Extension: V-Model

* **Multiple testing** to each level
* Acceptance/System/Integration/Unit Test

## Waterfall Extension: Spiral Model

* Prototyping + Waterfall
* 4 phase
    * Determine objects / Requirements plan
    * Identify and resolve risk -> working prototype
    * Dev & Test
    * Plan for next iteration
* Higher cost
* Non-trivial scope

## Agile Process

### Goals

- Adaptive planning
- Evolutionary development & delivery
- Time-boxed iteration
- **Rapid & flexible respnose to change**

## Agile Manifesto

- **Individuals & Interaction** over Processes & Tools
- **Working Software** over Comprehensive Documentation
- **Customer Collaboration** over Contract Negotiation
- **Responding to Change** over Following a Plan

### Agile Manifesto 12 Principles

1. Early and continuous delivery
2. Welcome changing requirements
3. Deliver working software frequently
4. Work together daily
5. Motivated individuals
6. Face to face conversation
7. Working software is the primary measure of progress
8. Constant pace
9. Continuous attention to technical excellence and good design
10. Simplicity
11. Self-organizing teams
12. Reflects on how to become more effective, tunes the behavior at regular intervals

## Waterfall vs. Agile

|  | Waterfall | Agile |
| --- | --- | --- |
| Timeline | Fixed | Fixed (short term) |
| Client Involvement | No | Promoted |
| Budget | Fixed | Flexible |
| Success Rate(Easy/Difficult) | High / Low (architect-centric) | High / High (member-centric) |
| Documentations | Good | Poor |

## Rational Unified Process (RUP)

- Unified Process → All steps of SDLC unified in each phase
- **Integration & Automation** is the key
- **Inception**: **What** to build
    - Prepare vision documents and initial business case
        - Including **risk assessment** and resource estimate
    - Develop **high-level project requirements**
    - Manage **project scope**
        - **Reduce risk** by **identifying all key requirements**
- **Elaboration**: **How** to build
    - **Detail requirements** as necessary (~80%)
    - Produce an **executable** and **stable architecture** (~10% code implemented)
    - Drive architecture with key use cases
    - Verify architectural quality (**reliability & scalability**)
    - Continuously assess business case, risk profile and development plan
- **Construction**: **Build**
    - Build daily / weekly + **automated** build & test
    - Deliver **fully functional** software
- **Transition**: Verify & Deploy to Users

### RUP Best Practice

- Adapt the process
- Balance stakeholder priorities
- Collaborate across teams
- Demonstrate value iteratively
- Elevate the level of abstraction
- Focus continuously on quality

## Extreme Programming (XP)

- A set of principles that provides the **highest value** in the **fastest way** possible
- Most useful **when plan is completely unclear**

### XP 5 Values

- Communication
- Simplicity
- Feedback
- Courage
- Respect

### XP 12 Practices

- **Fine Scale Feedback**
    - **Pair Programming (PP)**
    - Planning Game
    - **Test Driven Development (TDD)**
    - Whole Team
- **Continuous Process**
    - **Continuous Integration (CI)**
    - Design Improvement
    - Small Release
- **Shared Understanding**
    - **Coding Standards**
    - **Collective Code Ownership**
    - Simple Design
    - System Metaphor
- **Programming Welfare**
    - **Sustainable Pace**

### XP Roles

- Customer
- Developer
- Coach
- Tracker

## Pair Programming (PP)

- **Code Review to Extreme Level → Real-Time Review**
- **Driver** (Code) & **Navigator** (Review)

## Scrum

- Lightweight framework that helps generate value through adaptive solutions
- **Sprint**: unit cycle of iteration
- Sprint goal consists of **working product**

### 3 Scrum Goals

- Transparency
- Inspection
- Adaptation
