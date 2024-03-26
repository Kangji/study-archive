# DDL: Database Metadata

With engines and SQL execution down, we are ready to begin some Alchemy.
The central element of both SQLAlchemy Core and ORM is the SQL Expression Language which allows for fluent, composable construction of SQL queries.
The foundation for these queries are Python objects that represent database concepts like tables and columns.
These objects are known collectively as database metadata.

The most common foundational objects for database metadata in SQLAlchemy are known as MetaData, Table, and Column.
The sections below will illustrate how these objects are used in Core-oriented style.

## `Metadata` Object

`Metadata` object is a facade around a python dictionary that stores a series of `Table`s keyed to their string name.
Each `Table` object belongs to a single `Metadata` object.

```py
metadata = Metadata()
```

Having a single `MetaData` object for an entire application is the most common case,
represented as a module-level variable in a single place in an application,
often in a “models” or “dbschema” type of package.
It is also very common that the `MetaData` is accessed via an ORM-centric registry or `DeclarativeBase` base class,
so that this same `MetaData` is shared among ORM- and Core-declared Table objects.

There can be multiple `MetaData` collections as well;
`Table` objects can refer to `Table` objects in other collections without restrictions.
However, for groups of `Table` objects that are related to each other,
it is in practice much more straightforward to have them set up within a single `MetaData` collection,
both from the perspective of declaring them, as well as from the perspective of DDL (i.e. CREATE and DROP) statements being emitted in the correct order.

## `Table` Object

Once we have a `MetaData` object, we can declare some `Table` objects.
When not using ORM Declarative models at all, we construct each `Table` object directly,
which strictly follows the structure of `CREATE` statement,
typically assigning each to a variable that will be how we will refer to the table in application code:

```py
user_table = Table(
    "users",
    metadata,
    Column("id", Integer, primary_key=True, autoincrement=True),
    Column("name", String(30), nullable=False),
    Column("dept_id", ForeignKey("dept.id"), nullable=False),  # Foreign Keys can omit SQL type.
    CheckConstraint("id > 0"),
)

dept_table = Table(
    "dept",
    metadata,
    Column("id", Integer, primary_key=True, autoincrement=True),
    Column("name", String(63), nullable=False, unique=True)
)
```

All column objects are stored in `Table.c` attribute.

```py
>>> user_table.c.name
Column('name', String(length=30), table=<user_account>)

>>> user_table.c.keys()
['id', 'name', 'fullname']
```

### Integrity Constraints

Column constraints are provided by column argument,
while table constraints are provided by table schema items.

## Emitting DDL to DB

```py
metadata.create_all(engine)
metadata.drop_all(engine)
```

## Table Reflection

This is a reverse operation of table emission.

```py
user_table = Table('users', metadata, autoload_with=engine)
```
