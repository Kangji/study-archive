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
    * Relationships are lazy.

### `AsyncAttrs`

Since relationships are lazy, it must not be used in async.
Instead, `Base` with `AsyncAttrs` supports `awaitable_attrs` field to access relationships asynchronously.

```py
dept: Department = await user.awaitable_attrs.dept
```
