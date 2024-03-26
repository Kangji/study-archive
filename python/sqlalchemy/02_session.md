# Session

Since connection is core-centric, which is focused on sql, session is preferred to connection.
Session is a facade that internally uses connection and construct SQL using ORM.

In the most general sense, the `Session` establishes all conversations with the database and represents a “holding zone” for all the objects which you’ve loaded or associated with it during its lifespan.
It provides the interface where SELECT and other queries are made that will return and modify ORM-mapped objects.
The ORM objects themselves are maintained inside the `Session`, inside a structure called the identity map - a data structure that maintains unique copies of each object,
where “unique” means “only one object with a particular primary key”.

The `Session` in its most common pattern of use begins in a mostly stateless form.
Once queries are issued or other objects are persisted with it,
it requests a connection resource from an `Engine` that is associated with the Session,
and then establishes a transaction on that connection.
This transaction remains in effect until the `Session` is instructed to commit or roll back the transaction.
When the transaction ends, the connection resource associated with the `Engine` is released to the connection pool managed by the engine.
A new transaction then starts with a new connection checkout.

The ORM objects maintained by a `Session` are instrumented such that whenever an attribute or a collection is modified in the Python program,
a change event is generated which is recorded by the `Session`.
Whenever the database is about to be queried, or when the transaction is about to be committed,
the `Session` first flushes all pending changes stored in memory to the database.
This is known as the **unit of work pattern**.

When using a `Session`, it’s useful to consider the ORM mapped objects that it maintains as proxy objects to database rows,
which are local to the transaction being held by the `Session`.
In order to maintain the state on the objects as matching what’s actually in the database,
there are a variety of events that will cause objects to re-access the database in order to keep synchronized.
It is possible to “detach” objects from a Session, and to continue using them, though this practice has its caveats.
It’s intended that usually, you’d re-associate detached objects with another `Session` when you want to work with them again,
so that they can resume their normal task of representing database state.

```py
# Connection-like execution
with Session(engine) as session:
    session.execute(...)
    session.commit()
async with AsyncSession(async_engine) as async_session:
    await async_session.execute(...)
    await async_session.commit()
# SQL Operations using ORM
with Session(engine) as session:
    session.add(dept)
    session.delete(dept)
    session.commit()
```

## Querying

The difference with `Connection` and `Session` is that `Session` can return mapped class constructed by row. For example:

```py
stmt = select(User).where(User.id == 1)
with engine.connect() as conn:
    row = conn.execute(stmt).fetchone()     # row: Row = ({id}, {name}, ...)
    row = conn.scalars(stmt).first()        # row: Any = {id}
with Session(engine) as session:
    row = session.execute(stmt).fetchone()  # row: Row = ({User},)
    row = session.scalars(stmt).first()     # row: Any = {User}
```
