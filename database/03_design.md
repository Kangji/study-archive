# Database Design

## Database Design Phase

* **Conceptual Design**
  * *Provide E-R Schema*: description of requirements
  * Not an implementation model
* **Logical Design**
  * *Map to the data implementation model of DBMS*
  * e.g., Relation Schema
* **Physical Design**
  * *Specify physical features of database*
  * e.g., index

## E-R Model

* Entity: object
  * Entity Set: Set of entities of same type that shares the property.
* Relationship: association among two or more entities
  * Relationship Set: Set of relationships
* Attribute: properties of entity or relationship

## E-R Diagram

### Style 1

* Rectangle: entity set
* Diamond, connected with rectangles by line/arrow: Relationship set
  * Double diamond: identifying relationship set of weak entity set
  * Line: entity from opposite set can be mapped to many entities of this set
  * Arrow: entity from opposite set can be mapped to one entity of this set
* Listed items in rectangle: attributes of entity
  * Underlined attributes: PK
  * Dash-underlined attributes: discriminator
  * Indentation: composite attribute
  * Curly bracket: multivalued attribute
  * `()`: derived attribute
* Small box connected to diamond with dashed line: Attributes of relationship
* Written text on line/arrow: role of the participating entity
* Double line/arrow: total participation
* Rectangle pointing other rectangle: generalization/specialization

### Style 2

다른 점만 기술함

* Attribute: pin
  * PK of entity set: black pin
  * composite attribute: pin on pin
* Generalization/Specialization
  * Disjoint: colored arrow
  * Overlapping: uncolored arrow

## Attribute Types

* Simple vs Composite
  * Simple: atomic
  * Composite: composed of multiple parts
  * Why composite? It is more intuitive and better represents the requirement.
* Single-Valued vs Multivalued
  * Single-valued: each entity has a single value for the attribute
  * Multivalued: each entity has multiple values for the attribute
* Derived
  * Value of an attribute can be derived from other attributes or entities

## Degree of Relationship Set

*How many entity sets are involved* is called **degree** of relationship set.
Most relationships are binary, but not all (e.g., ternary).

A binary relationship whose involved entity sets are the same is
sometimes called **unary**.

## Mapping Constraints

### Relationship Cardinality

A number of entities that can be mapped via a relationship.
In other words, *how many relationships each entity can participate*.

* one-to-one (1:1)
  * entity from A mapped to *at most one* entity from B (cardinality 1)
  * vice versa
* one-to-many (1:m)
  * entity from A can be mapped to *many entity* from B (cardinality m)
  * entity from B mapped with *at most one* entity from A (cardinality 1)
* many-to-one (m:1): opposite of one-to-many
* many-to-many (m:n): no constraint

In diagram, arrow means one, line means many.

* $A \leftarrow R - B$: one-to-many(1:m). c(A) = m, c(B) = 1.
* $A \leftarrow R \rightarrow B$: one-to-one(1:1). c(A) = 1, c(B) = 1.
* $A - R - B$: many-to-many(m:n). c(A) = n, c(B) = m.
* $A - R \rightarrow B$: many-to-one(m:1). c(A) = 1, c(B) = m.

### Participation

* **Total participation**
  * *every entity participates in at least one relationship*
  * opposite: **Partial participation**

In diagram, double line means total participation, regardless of cardinality.

### Alternative Notation

Express cardinality by upper & lower limits.
Write on the line.

* Max m (or `*`): many
* Max 1: one
* Min 0: partial participation
* Min 1: total participation

## Keys

* Entity Set: Same as relational model
  * Superkey, Candidate key, Primary Key
  * Set of attributes whose values can distinguish entities from each other
* Relationship Set: Combination of PKs from entity sets form superkey
  * Candidate key, Primary key: based on mapping constraints

## Redundant Attributes

Relational Model에서와 다르게 entity간 relationship을 explicit하게 표현 가능.
따라서 foreign key에 해당하는 attribute는 redundant => should be removed

## Weak Entity Set

An entity set that does not have sufficient attributes to form PK.
PK에 redundant attribute가 포함되는 경우.
반대는 strong entity set.

Redundant attribute는 foreign key,
즉 다른 entity(**identifying entity set**)의 attribute이므로,
이와 연결해주는 **identifying relationship set**을 표시해야 함. (double diamond)

Weak entity set은 항상 total participation + strong entity set과 one-to-many.

PK - redundant attribute(s) = **discriminator**, or **partial key**

## Specialization / Generalization

* Specialization: entity set을 여러 그룹으로 쪼개어 sub entity set을 만드는 것
  * 상속: super entity set의 attribute들이 상속됨 + own attribute를 가질 수 있음
* Generalization: 여러 entity set들을 합쳐 higher-level entity를 만드는 것
  * 디자인 과정에서 specialization의 반대로 진행되나, 결과만 놓고 보면 똑같음.

### Types of Generalization

* Disjoint: sub entity set들끼리 disjoint
* Overlapping: 어떤 entity는 여러 sub entity set에 포함됨
* Total: 모든 entity가 적어도 한 sub entity set에 포함됨
* Partial: sub entity set에 포함되지 않는 entity가 있을 수 있음

## (Logical Design) Reducing E-R Schema to Relation Tables

* Entity Set => table
  * columns = attributes + one-to-many 당한 foreign key들
* Relationship Set => table (only if many-to-many)
  * columns = attributes + participating entity들의 pk
  * one-to-one이나 one-to-many는 relationship을 foreign key로 표현
* Generalization/Specialization => 2 options
  * foreign key로 표현(sub entity sets reference super entity set)
    * Partial and/or Overlapping generalization에 적합
  * sub entity set들 각각이 super entity set의 attribute를 가지도록 merge
    * Total and/or Disjoint generalization에 적합

## Design Issues

### Entity vs Attribute

* Simple => attribute
* Complex => entity
  * Entity는 다른 entity와 relationship을 가질 수 있음, 즉 독립적인 객체로서 생각할 수 있음
  * Multivalue attribute / shared value라면 entity로 만드는 것 고려 해볼만

### Entity vs Relationship

* Relationship: simple but limited
* Entity: 독립적인 객체로 다른 entity와 relationship을 가질 수 있음

### Binary vs n-ary

All n-ary relationships are representable by binary relationships,
by adding an entity set that represents n-ary relationship set.

항상 모든 것을 binary relationship으로 표현하는 것은 diagram의 직관성을 오히려 해칠 수도 있다.
언제나 상황에 맞게 사용해야 함.

### Generalization/Specialization

굳이 안해도 될 것인가 할 것인가

## Design Quality

* Completeness: all relevant features are represented
* Correctness: concept of E-R model is properly used
  * Syntactic Correctness: concepts are properly defined
  * Semantic Correctness: Concepts are used according to their definition
