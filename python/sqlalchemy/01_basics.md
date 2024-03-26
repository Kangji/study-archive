# Basics

The SQLAlchemy SQL Toolkit and Object Relational Mapper is a comprehensive set of tools for working with databases and Python.
It has several distinct areas of functionality which can be used individually or combined together.
Its major components are illustrated below, with component dependencies organized into layers: ORM and core.

![SQLAlchemy Architecture](/python/sqlalchemy/assets/architecture.png)

Core contains the breadth of SQLAlchemy’s SQL and database integration and description services,
the most prominent part of this being the **SQL Expression Language**.

The SQL Expression Language is a toolkit on its own, independent of the ORM package,
which provides a system of constructing SQL expressions represented by composable objects,
which can then be “executed” against a target database within the scope of a specific transaction, returning a result set.
Inserts, updates and deletes (i.e. DML) are achieved by passing SQL expression objects representing these statements along with dictionaries that represent parameters to be used with each statement.

The ORM builds upon Core to provide a means of working with a domain object model mapped to a database schema.
When using the ORM, SQL statements are constructed in mostly the same way as when using Core,
however the task of DML, which here refers to the persistence of business objects in a database,
is automated using a pattern called unit of work,
which translates changes in state against mutable objects into INSERT, UPDATE and DELETE constructs which are then invoked in terms of those objects.
SELECT statements are also augmented by ORM-specific automations and object-centric querying capabilities.

Whereas working with Core and the SQL Expression language presents a schema-centric view of the database,
along with a programming paradigm that is oriented around immutability,
the ORM builds on top of this a domain-centric view of the database with a programming paradigm that is more explicitly object-oriented and reliant upon mutability.
Since a relational database is itself a mutable service, the difference is that Core/SQL Expression language is command oriented whereas the ORM is state oriented.

## Installation

Install sqlalchemy and database-related packages (e.g., `psycopg2` for PostgreSQL).

```sh
pip install sqlalchemy
pip install psycopg2
```

## Engine

The start of any SQLAlchemy application is an object called the **Engine**.
This object acts as a central source of connections to a particular database,
providing both a factory as well as a holding space called a connection pool for these database connections.
The engine is typically a global object created just once for a particular database server,
and is configured using a URL string which will describe how it should connect to the database host or backend.

```py
URL = f'{DBMS}+{DBAPI}://{USERNAME}:{PASSWORD}@{HOST}:{PORT}/{DBNAME}'
engine: Engine = create_engine(URL)
async_engine: AsyncEngine = create_async_engine(URL) # Async engine is just an asyncio proxy.
```

Note that the engine will lazily initialize the connection.

## Connection & Result

Create (async) connection with `connect()` method. By default, engine pools the connection.
(Async) connection is context manager: it will properly open and close the underlying connection.
Begins transaction on open and rollback unsaved changes on close.

`execute()` executes raw SQL statement with parameters and returns `Result` object.
`Result` is an iterator of `Row` objects. Each `Row` object acts as named tuple.

```py
with engine.connect() as conn:
    conn.execute(...)
    conn.commit()
async with async_engine.connect() as conn:
    await conn.execute(...)
    await conn.commit()
```

### `scalar()` & `scalars()`

These methods expect each row to be a scalar, that returns only first entry of each row.
Difference between two methods is that `scalar()` also expects the result to be a single row.
