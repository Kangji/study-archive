# SQL

## Basics

* Each SQL command is separated by a semicolon.
* Ignores redundant whitespaces (recommend to use them for visualizing).
* Two dashes (`--`) introduce comments.
  * Whatever follows them is ignored up to the end of the line.
* Single-quoted string literals.
* Case insensitive about keywords and identifiers
  * Except when identifiers are double-quoted to preserve the case.

## Domain Types

* `char(n)`: fixed length(n) string
* `varchar(n)`: variable length(up to n) string
* `int`: integer
* `smallint`: small integer
* `numeric(m, d)`: fixed point ($m \times 10^{-d}$)
* `real`, `double precision`: floating point
* `float(n)`: floating point of at least n digit precision
* Date and Time (ISO 8601) - dependent to dbms
  * `date`: `yyyy-mm-dd`
  * `datetime`: `yyyy-mm-dd hh:mm:ss[.mmm]`
  * `time`: `hh:mm:ss[.mmmmmm]`

## Create Table

```sql
CREATE TABLE table_name (
  { column_name domain_type { column_constraint }* },+
  { , table_constraint }*
);
```

Where column constraints are:

```sql
NOT NULL
UNIQUE
PRIMARY KEY
CHECK ( condition )
REFERENCES table_name { ( { column_name },+ ) }? { ON DELETE referential_action }? { ON UPDATE referential_action }?
```

Where table constraints are:

```sql
UNIQUE ( { column_name },+ )
PRIMARY KEY ( { column_name },+ )
CHECK ( condition )
FOREIGN KEY ( { column_name },+ ) REFERENCES table_name ( { column_name },+ ) { ON DELETE referential_action }? { ON UPDATE referential_action }?
```

Where referential actions are:

```sql
CASCADE
SET NULL
SET DEFAULT
```

### Integrity Constraints

**Integrity constraints** ensures correctness of data under specific business
logic and guard against accidental damage to the database.

* `NOT NULL`: value of the column must not be null
* `UNIQUE`: specified column(s) form candidate key
* `PRIMARY KEY`: specified column(s) form primary key
  * implies `NOT NULL` and `UNIQUE`
* `CHECK`: value of column or tuple must ensure the condition
* referential integrity: value of the column(s) must appear in the column(s) of another relation

### Referential Action

Actions to perform on event is called **referential action**.
`ON DELETE` and `ON UPDATE`.

* `CASCADE`: delete/update on referenced relation is cascaded
* `SET NULL`: set null
* `SET DEFAULT`: set default

## Drop Table

```sql
DROP TABLE table_name;
```

## Alter Table

```sql
ALTER TABLE table_name ADD column_name domain_type;
ALTER TABLE table_name DROP column_name;
```

## Select (Query)

Result of SQL Query is analogous to relation, except that duplicated row is allowed.

```sql
SELECT { DISTINCT }? { * | { expression { { AS }? output_name }? },+ }
FROM { from_item },+
{ WHERE condition }?
{ GROUP BY { grouping_element },+ }?
{ HAVING condition }?
{ ORDER BY { expression { ASC | DESC } },+ }?;
```

### Expression (`SELECT` Clause)

A select clause can contain any expressions, such as arithmetic expressions.

### Aggregate Function

* Takes values of a column of a relation and return single value.
* `NULL` values are ignored, except `count (*)`
* `avg`, `min`, `max`, `sum`, `count`
* `count` is little different
  * `count (DISTINCT column_name)`
  * `count (*)` counts the number of columns, including `NULL`.

### From Item (`FROM` Clause)

* `From item` includes but not limited to table.
* List of `from item`s corresponds to cartesian product.
* Joining two `from item`s are available
* Result of a subquery can be `from item`.
  * In this case the result of subquery requires an alias.

```sql
table_name { { AS }? alias { ( { column_alias },+ ) }? }?
from_item { NATURAL }? join_type from_item { ON join_condition }? { USING ( { column_name },+ ) }?
( subquery ) { AS } alias { ( { column_alias },+ ) }?
```

### Rename (`AS` Clause)

`AS` clause can rename expressions, `from item`s, etc.
`AS` keyword is optional.

* Called **tuple variables**
  * Renaming its columns is also possible.
* Called **correlation variables** when used in correlated subquery.

Tuple variables are included in the local context of query,
so that later it can be referenced.

### Condition (`WHERE` Clause)

Corresponds to selection predicate of the relational algebra.
Various operators, functions and predicates are supported.

#### Logical Operators

* Three predicates `AND`, `OR`, `NOT`
* Three valued logic -> Three boolean values `TRUE`, `FALSE`, `NULL`.
  * As mentioned, `NULL` is a member of ever domain, including boolean.
* Logical operation with `NULL` returns `NULL` except
  * `FALSE AND NULL` (= `FALSE`)
  * `TRUE OR NULL` (= `TRUE`)
* Where clause treats `NULL` as `FALSE`

#### Comparison Operators

* `<`, `>`, `=`, `<=`, `>=`, `!=`(or `<>`)
* Comparison with `NULL` returns `NULL`, since `NULL` represents unknown.

#### Comparison Predicates

* `e1 BETWEEN e2 AND e3`: equivalent to `e1 >= e2 AND e1 <= e3`
  * Range inclusive
* `IS`: always return `TRUE` or `FALSE` by treating `NULL` as comparable value
  * e.g., `e IS NULL`

#### String Predicates

* `LIKE`: pattern matching
  * `%`: any substring
  * `_`: any character
  * e.g., `title LIKE '%data%'`: any pattern which includes 'data' substring

### Grouping (`GROUP BY` Clause)

Groups the tuples.

* Aggregate function aggregates column values by group.
* Non-grouping attributes must appear in `SELECT` with aggregate function.

#### `HAVING` Clause

Condition applied after grouping. `WHERE` clause is applied before grouping.

`HAVING` clause can be replaced by subquery in `FROM` clause. (Better performance)

```sql
SELECT a, avg(b)
FROM tbl
GROUP BY a
HAVING avg(b) > 500;
-- is equivalent to
SELECT a, avg_b
FROM (SELECT a, avg(b) AS avg_b
      FROM tbl
      GROUP BY a)
WHERE avg_b > 500;
```

### Ordering

Sort the result. The last step of query operation.

## Nested Query (Subquery)

**Subquery** is a nested **select-from-where** expression.
Common usecase: `from item`, set operation / membership / comparison

### Set Operations

* Idential to set operations in relational algebra.
  * `UNION`, `INTERSECT`, `EXCEPT`
* Operates on relation(query result).
  * e.g., `(select name from instructors) union (select name from students)`
* Automatically eliminates duplicate rows
  * `UNION ALL`, `INTERSECT ALL`, `EXCEPT ALL` preserves duplicates.
  * when m, n duplcate rows,
    * `UNION ALL`: m + n
    * `INTERSECT ALL`: min(m, n)
    * `EXCEPT ALL`: min(0, m - n)

### Set Membership

* Logical operation whether tests a tuple is in a relation (usually subquery).
  * `in`, `not in`
  * e.g.,`(id, name) in (select id, name from dept where no = '301')`

### Set Comparison

* `some`: tuple exists in a relation which satisfies condition.
* `all`: all tuple in a relation satisifies condition.
  * e.g., `score > some (select score from student where name like '%ab%')`
* `exists`: whether relation is not empty.
  * e.g., `exists (select * from student where name like '%ab%')`
* `not exists`: whether relation is empty.

## Join

```sql
from_item { NATURAL }? join_type from_item
{ ON join_condition }?
{ USING ( { column_name },+ ) }?
```

### Join Condition

Which tuples in each relations match, and what attributes are present in the result.

* `Natural`: use all columns with same name, retain only one copy
  * Must be careful
* `ON` Clause: specify the join condition
* `USING` Clause: specify which common columns to test for equality

### Join Type

How tuples in each relation that don't match any in the other are treated.

* (Inner) Join: Unmatched tuples are discarded
* Left Outer Join: Unmatched tuples from left relation are filled with null
* Right Outer Join: Unmatched tuples from right relation are filled with null
* Full Outer Join: Unmatched tuples from both relation are filled with null

## Insert Into

```sql
INSERT INTO table_name { ( { column_name },+ ) }? insert_values;
```

Where insert values are:

```sql
VALUES { ( { expression },+ ) },+
subquery
```

## Update

```sql
UPDATE table_name
SET { { column_name = expression | ( { column_name },+ ) = ( { expression },+ ) } },?
{ WHERE condition }?;
```

## Delete From

```sql
DELETE FROM table_name
{ WHERE condition }?;
```

## Role and Privilege

**Roles** represent the group of users and their privileges.

Grant/Revoke:

* privilege to/from role
* privilege to/from user
* role_A to/from role_B (role_B implies role_A)
* role to/from user

Privileges:

* `select`
* `insert`
* `update`
* `delete`
* `resources`: create table
* `index`: create index
* `alteration`: alter
* `drop`
* `all { privileges }?`

```sql
CREATE ROLE role_name;
GRANT { { privilege },+ ON table_name | role_name }
TO { public | { user_or_role_name },+ };
REVOKE { { privilege },+ ON table_name | role_name }
FROM { user_or_role_name },+;
```

## Discussion

### 3-1. Integrity Constraint

Q. What is an integrity constraint?

* relation의 tuple들이 항상 만족해야 하는 조건
* constraint가 깨지면 의도하지 않은 일이 발생할 수 있음

### 3-2. Integrity Constraint w/ Create

Q. Why are integrity constraint declarations,
such as primary key and foreign key,
part of the create table statement instead of, say,
the select or insert statement?

A. Integrity constraint는 relation의 속성이기 때문.
반면 Select나 Insert는 relation의 tuple과 관련 있는 command.
