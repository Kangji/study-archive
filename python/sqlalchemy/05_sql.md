# DML: SQL Expression

You can write SQL using SQL expressions and mapped classes or `Table` objects,
just like writing raw SQL statement.

## Insert

```py
# Statement without parameters
stmt = insert(Dept)
session.execute(stmt, [{'name': 'CSE'}, {'name': 'CLS'}])
# Statement with parameters
stmt = insert(Dept).values({'name': 'CSE'}, {'name': 'CLS'})
session.execute(stmt)
# Insert-Returning
stmt = insert(Dept).returning(Dept.id, Dept.name)
# Insert-Select-From
stmt = insert(Dept).from_select(['name'], Dept.__table__)
```

## Select

```py
# select dept.id, dept.name from dept (from clause inferred)
stmt = select(Dept)
# explicit select clause
stmt = select(1)
# explicit from clause
stmt = select(Dept).select_from(Dept)
# join users on dept.id == users.dept_id (on clause inferred)
stmt = select(Dept).join(User)
# explicit on clause
stmt = select(Dept).join(User, Dept.id == User.dept_id)
# left outer join
stmt = select(Dept).join(User, Dept.id == User.dept_id, isouter=True)
# full outer join
stmt = select(Dept).join(User, Dept.id == User.dept_id, full=True)
# where clause using and/or
stmt = select(Dept).where(or_(Dept.name == 'CSE', Dept.name == 'CLS'), Dept.id == 1)
# order by name desc
stmt = select(Dept).order_by(Dept.name.desc())
# Aggregate Functions
stmt = select(func.count(User.id))
# group by name + having
stmt = select(Dept).group_by(Dept.name).having(func.count(User.id) > 1)
# set operations (union, intersect, except_, union_all, intersect_all, except_all)
stmt = union(stmt1, stmt2)
# exists subquery
stmt = select(Dept).where(stmt.exists())
```

### Subquery

`Subquery` object represents a subquery, distinguished with `Select`.
Call `subquery()` method of `Select` to make it subquery, or `alias()` to make it subquery with alias.
`scalar_subquery()` is preferred when the query returns scalar.

## Update

```py
# Statement without parameters
stmt = update(Dept).values(id=1, name='CLS').where(Dept.name == 'CSE')
session.execute(stmt)
# Statement with parameters: use bindparam()
stmt = update(Dept).values(name=bindparam('newname')).where(Dept.name == bindparam('oldname'))
session.execute(stmt, [{'newname': 'CLS', 'oldname': 'CSE'}, ...])
# Update-Returning
stmt = update(Dept).values(id=1).returning(Dept)
```

## Delete

```py
stmt = delete(Dept).where(name='CSE')
```

## Aliases

All `FromClause` items such as `Table` object or `select` statement can be aliased with `alias()` method.

```py
stmt = select(Department).alias('foo')  # alias method of select query returns subquery object
stmt = select(Department.__table__.alias('foo'))    # select * from dept as foo
```
