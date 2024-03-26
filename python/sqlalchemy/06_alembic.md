# Migration with Alembic

Alembic is a lightweight database migration tool for usage with the SQLAlchemy Database Toolkit for Python.

## Installation

```sh
pip install alembic
alembic init .
```

## Project Structure

```txt
yourproject/
    alembic/
        env.py
        README
        script.py.mako
        versions/
            3512b954651e_add_account.py
            2b1ae634e5cd_add_order_id.py
            3adcc9a56557_rename_username_field.py
    alembic.ini
```

* `env.py`: Python script that is run whenever the alembic migration tool is invoked. At the very least, it contains instructions to:
    * configure and generate a SQLAlchemy engine
    * procure a connection from that engine along with a transaction
    * invoke the migration engine, using the connection as a source of database connectivity
* `script.py.mako`: Mako template file which is used to generate new migration scripts. Whatever is here is used to generate new files within `versions/`.
* `versions/`: Holds the individual version scripts. Files don’t use ascending integers, and instead use a partial GUID approach. In Alembic, the ordering of version scripts is relative to directives within the scripts themselves, and it is theoretically possible to “splice” version files in between others, allowing migration sequences from different branches to be merged, albeit carefully by hand.
* `alembic.ini`: Alembic configuration file. It can be overwritten by running `env.py`. All fields are stored in `context.config` global variable.

## Migration Script

Create a revision.

```sh
alembic revision [--autogenerate] -m <Revision Name>
```

Then fill `upgrade()` and `downgrade()` function in the script unless you use `--autogenerate` option.
Autogeneration is based on your project's sqlalchemy metadata. See `env.py`.

### Autogeneration

Autogeneration will detect:

* Table additions, removals.
* Column additions, removals.
* Change of nullable status on columns.
* Basic changes in indexes and explicitly-named unique constraints
* Basic changes in foreign key constraints
* Major changes of column type (e.g., int -> varchar)

Autogeneration will not detect:

* Changes of table/column name.
* Anonymously-named constraints

### Alembic Operations

See [operation reference](https://alembic.sqlalchemy.org/en/latest/ops.html).

## Run Migration

```sh
# Make database up-to-date.
alembic upgrade head
# Make database back to nothing.
alembic downgrade base
# Upgrade/Downgrade into specific revision.
alembic (upgrade | downgrade) {revision-id}
# Upgrade/Downgrade with relative identifiers
alembic upgrade +2
alembic downgrade -1
```

Migrations are run based on current revision stored in database.
You can also check current revision.

```sh
alembic current
```
