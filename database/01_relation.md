# Relational Model

## Relation

$r \subseteq D_1 \times D_2 \times \cdots \times D_n$

Given sets $D_1, D_2, \cdots, D_n$, **relation** $r$ is a *subset* of
$D_1 \times D_2 \times \cdots \times D_n$

## Relation Schema

Given attributes $A_1, A_2, \cdots, A_n$,
$R = (A_1, A_2, \cdots, A_n)$ is a **relation schema**.
(*Structure of relation*)

$r(R)$ is a relation (variable) on the schema $R$.

## Relation Instance

The *current values of a relation* are called **relation instance**.
A relation is specified by a table.
An element $t$ of relation $r$ is a tuple, represented by a *row*.

## Domain

A *set of allowed values* for each attribute is called **domain**.
Denoted by $D_1, D_2, \cdots$ for each attribute $A_1, A_2, \cdots$.

## Atomic

Attribute values are usually required to be **atomic** (*indivisible*).

## Null

**Null** represents *unknown or missing*, which is a member of every domain.

## Unordered

Relations are **unordered** sets.

## Relational Database

A database *consistes of multiple relations*.

## Keys

Key $K \subseteq R$

### Superkey

$S = \{K \subseteq R | K \rightarrow R\}$

*Sufficient to identify a unique tuple* of possible relation instance $r(R)$.

### Candidate Key

$C = \{K \subseteq R | \forall \alpha \subset K, \alpha \notin S\}$

*Superkey which is minimal*.

### Primary Key

The *most representative candidate key* is selected to be **primary key**.

### Foreign Key

Value in one relation *must appear in another*. (Referencing/Referenced)

## Relational Algebra

* Selection: $\sigma_{\rho}(r) = \{t \in r | \rho(t) \}$
  * Selection of tuples
  * **Selection predicate** $\rho$
* Projection: $\Pi_{A_1, A_2, \cdots, A_k}(r)$
  * Selection of columns.
  * Duplicated rows removed from result.
* Union: $r \cup s = \{t | t \in r \lor t \in s\}$
  * **Compatibility**: *same **arity*** (# of attributes) & *compatible domain*
* Set Difference: $r - s = \{t | t \in r \land t \notin s\}$
  * Must be compatible.
* Cartesian Product: $r \times s = \{tq | t \in r \land q \in s\}$
  * If $R \cap S \ne \phi$, renaming attribute is required.
* Rename: $\rho_{N}(E)$ returns the *expression $E$ under name $N$*.
  * $\rho_{N (A_1, \cdots, A_k)}(E)$ also renames the attributes.
* Intersection: $r \cap s = \{t | t \in r \land t \in s\}$
  * Must be compatible.
  * $r \cap s = r - (r - s)$
* Natural Join: $(r \Join s)(R \cup S)$, where $r(R)$ and $s(S)$
  * $t[R] = t_r$, $t[S] = t_s$
  * $r \Join s = \Pi_{R \cup S}(\sigma_{r.(R \cap S) = s.(R \cap S)}(r \times s))$
  * commutative & associative => trivial (reflexivity)
  * $R \cap S = \phi \Rightarrow r \Join s = r \times s$  
    *Pf*. Suppose $R \cap S = \phi$.  
    By definition of join,
    $r \Join s = \Pi_{R \cup S}(\sigma_{r.(R \cap S) = s.(R \cap S)}(r \times s)) = r \times s$
  * $R = S \Rightarrow r \Join s = r \cap s$  
    *Pf*. Suppose $R = S$.  
    By definition of join,
    $r \Join s = \Pi_{R \cup S}(\sigma_{r.(R \cap S) = s.(R \cap S)}(r \times s)) = \Pi_R(\theta_{r.R = s.R}(r \times s)) = r \cap s$
  * Theta Join: $r \Join_\theta s = \sigma_\theta(r \times s)$
* Assignment: $temp \leftarrow some\_query$
  * assign temporary variable to complex expression
  * write expression as a sequence consisting assignments

## Discussion

### 2-1. Candidate Key

Q. Why do you think “key”, without any qualifier, is meant to be candidate key instead of super key?

A. Non-candidate superkey is inefficient since the record is still identifiable with its subset, which is smaller.

### 2-2. Foreign Key Constraint

Q. Give examples of inserts and deletes, which can cause a violation of the foreign key constraint.

* Insert: Cannot insert non-existing foreign key value
* Delete: Cannot delete referenced column unless handled

### 2-3. Name as SuperKey

Q. In general, can we use `name` attribute as a superkey?

A. Usually name is not unique. Possible duplication in the future.

### 2-11. Operator Precedence

|Precedence|Operators|Associativity|
|:-:|:-:|:-:|
|0|parenthesis||
|1|unary: $\rho$, $\sigma$, $\Pi$||
|2|multiplication without compatibility: $\times$, $\Join$|left to right|
|3|multiplication under compatibiltiy: $\cap$|left to right|
|4|addition under compatiblity: $-$, $\cup$|left to right|
