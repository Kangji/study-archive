# Python

Python language spec 및 library spec을 정리한 archive입니다.

## [Amaranth](amaranth.md)

The Amaranth hardware description language is a Python library for register transfer level modeling of synchronous logic.

To see official reference, please refer to [amaranth reference](https://amaranth-lang.org/docs/amaranth/latest/intro.html).

## Python Runtime Environment

Python Runtime 환경에 관한 정리입니다. Python 코드를 작성하는 것은 Reference에서 다룹니다.

* [Virtual Environment](runtime/01_venv.md)
* [CPython](runtime/02_cpython.md)
* [Global Interpreter Lock](runtime/03_gil.md)
* [Pyenv](runtime/04_pyenv.md)
* [Pipx](runtime/05_pipx.md)
* [Poetry](runtime/06_poetry.md)

## Python Language Reference

Python language spec을 다룹니다.

* [Namespace](reference/01_namespace.md)
* [Object Abstraction](reference/02_object.md)
* [Type Object](reference/03_type.md)
* [Metaclass](reference/04_metaclass.md)
* [Attribute](reference/05_attribute.md)
* [Descriptor](reference/06_descriptor.md)
* [Method Resolution Order](reference/07_mro.md)
* [Coroutine](reference/08_coroutine.md)
* [Iterator](reference/09_iterator.md)
* [Generator](reference/10_generator.md)
* [Context Manager](reference/11_context.md)
* [Built-in Items](reference/12_builtins.md)
* [Special Attributes and Methods](reference/13_special_methods.md)
* [Abstract Base Class](reference/14_abc.md)
* [Class Creation](reference/15_class.md)
* [Closure](reference/16_closure.md)
* [Decorator](reference/17_decorator.md)

## FastAPI

Python Web Framework인 FastAPI [tutorial](https://fastapi.tiangolo.com/learn/)을 다룹니다.

* [Basics](fastapi/01_basics.md)
* [Path Operation](fastapi/02_path_operation.md)
* [Dependency](fastapi/03_dependency.md)
* [Error Handling](fastapi/04_error_handling.md)
* [Web Security](fastapi/05_security.md)
* [WebSocket](fastapi/06_websocket.md)
* [Testing](fastapi/07_testing.md)
* [Other Stuff](fastapi/08_others.md)

## SQLAlchemy

Python SQL toolkit인 SQLAlchemy [documentation](https://docs.sqlalchemy.org/en/20/)을 다룹니다.

* [Basics](sqlalchemy/01_basics.md)
* [Session](sqlalchemy/02_session.md)
* [Metadata](sqlalchemy/03_metadata.md)
* [ORM Mapped Class](sqlalchemy/04_declarative.md)
* [SQL Expression](sqlalchemy/05_sql.md)
* [Migration with Alembic](sqlalchemy/06_alembic.md)

## Python Testing Frameworks

Python의 다양한 testing framework 및 tool들을 다룹니다.

* [Unittest](test/01_unittest.md)

## [Ruff](ruff.md)

An extremely fast Python linter and code formatter, written in Rust.
As can be inferred from being made in Rust, its usage is very similar to cargo.

To see official documentation, please refer to [Ruff documentation](https://astral.sh/ruff).

## Django

Python Web Framework인 Django [docs](https://docs.djangoproject.com/en/5.0/)를 다룹니다.

* [Django Project Overview](django/01_overview.md)
* [Django App Overview](django/02_app.md)
* [Models](django/03_model.md)
* [Queries](django/04_query.md)
* [Rendering the Responses](django/05_rendering.md)
* [Django Security]
* [Django Admin]
* [Django Auth]
* [Django Cache]
* [Django Logging]
