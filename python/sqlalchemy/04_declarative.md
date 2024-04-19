# DDL: ORM Mapped Class

When using the ORM, the process by which we declare `Table` metadata is usually combined with the process of declaring mapped classes.
The mapped class is any Python class we’d like to create, which will then have attributes on it that will be linked to the columns in a database table.
While there are a few varieties of how this is achieved, the most common style is known as declarative,
and allows us to declare our user-defined classes and `Table` metadata at once.

```py
class Base(DeclarativeBase): ...
# Base with asyncio support
class Base(AsyncAttrs, DeclarativeBase): ...

metadata = Base.metadata
```

## ORM Mapped Class

With the Base class established, we can now define ORM mapped classes.
ORM mapped class will internally construct `Table` object associated with `Base.metadata`.

```py
class User(Base):
    __tablename__ = "users"
    __table_args = (CheckConstraint("true"),)

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = Column(String(30), nullable=False)
    dept_id: Mapped[int] = mapped_column(ForeignKey("dept.id", ondelete="CASCADE"), nullable=False)

    dept: Mapped[Department] = relationship(back_populates="users")

class Department(Base):
    __tablename__ = "dept"

    id: Mapped[int] = Column(Integer, primary_key=True)
    name: Mapped[str] = Column(String(30), nullable=False)

    users: Mapped[list[User]] = relationship(back_populates="dept")
```

* Use `__table_args__` to provide additional arguments to table construct, such as table constraints.
* Use `mapped_column` just like using `Column` to define column. `mapped_column` is smarter.
    * It can infer SQL type with type annotation.
    * It can infer nullable with type annotation.
* `User.dept` and `Department.users` are relationships.
    * `back_populates` indicates the name of relationship attribute on the related class, which will be synchronized with this one.
    * By default, relationships are lazy.

Many-to-many relationships are represented by intermediary or secondary table.
You can pass the table metadata as `secondary` argument.

```py
class EmployeeProject:
    __table_name__ = "employee_project"
    ...

class Project:
    __table_name__ = "project"
    ...

class Employee:
    __table_name__ = "employee"
    projects: Mapped[list[Project]] = relationship(secondary=EmployeeProject.__table__)
```

## Loading Relationships

Since relationships are lazy, the query does not perform unncessary join and optimize the performance.
However, it becomes worse when accessing related attribute, which generates another SQL query.

It might cause "N+1" problem, or block the coroutine in async.
To address this, you might want to configure the query with **loader strategies**.

Loader strategies can be passed by the argument of `relationship`,
or `option` method of the query.

### Selectin Load

Selectin Load will ensure that a particular collection for a full series of objects are loaded up front using a single query.
It does this using a `SELECT` form that in most cases can be emitted against the related table alone,
without the introduction of `JOIN`s or subqueries, and only queries for those parent objects for which the collection isn’t already loaded.

In summary, Selectin Load will emit at most two queries, that second query uses `WHERE {fkey} IN (?, ?, ...)` condition to fetch all related objects.

```py
# relationship argument
class User:
    addresses: Mapped[list[Address]] = relationship(back_populates="user", lazy="selectin")

# option method
from sqlalchemy.orm import selectinload
stmt = select(User).options(selectinload(User.addresses)).order_by(User.id)
```

Selectin Load is preferred to Joined Load for one-to-many/many-to-many relationships.

### Joined Load

Joined Load is an eager load strategy that utilizes the `JOIN` clause.

```py
# relationship argument
class User:
    addresses: Mapped[list[Address]] = relationship(back_populates="user", lazy="joined")

# option method
from sqlalchemy.orm import joinedload
stmt = select(User).options(joinedload(User.addresses)).order_by(User.id)
```

Joined Load is preffered to Selectin Load for many-to-many relationships.

### `AsyncAttrs`

Rather than using loader strategy, another option to handle async queries is `AsyncAttrs`.
`Base` with `AsyncAttrs` supports `awaitable_attrs` field to access relationships asynchronously.

```py
dept: Department = await user.awaitable_attrs.dept
```
