# Poetry

Poetry is a tool for dependency management and packaging in Python.
It allows you to declare the libraries your project depends on and it will manage (install/update) them for you.
Poetry offers a lockfile to ensure repeatable installs, and can build your project for distribution.

Poetry aims to provide the interface as similar as Rust Cargo.

To see official documentation, please refer to [Poetry documentation](https://python-poetry.org/docs/).

## Installation

Poetry는 프로젝트의 dependency와 runtime을 관리하기 위한 tool이므로 특정 project에 의존하는 application이 아닌 점에 주의하자.
따라서 local environment에 설치하거나 pipx로 설치하자.

```sh
pipx install poetry
```

## Configuration

Like `git config`, poetry configuration is managed by `poetry config`.

```sh
poetry config --list
poetry config warnings.export false
poetry config virtualenvs.in-project true  # Recommended
```

## Project Setup

```sh
poetry new /path/to/project
```

This will create the project directory like:

```txt
your-project
├── pyproject.toml
├── README.md
├── your-project
│   └── __init__.py
└── tests
    └── __init__.py
```

The `pyproject.toml` file is what the most important here.
This will orchestrate your project and its dependencies.
You must explicitly specify the python version.
For now, it looks like this:

```toml
[tool.poetry]
name = "poetry-demo"
version = "0.1.0"
description = ""
authors = ["Author <author@author.com>"]
readme = "README.md"
packages = [{include = "poetry_demo"}]

[tool.poetry.dependencies]
python = "^3.7"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

However, there are several ways to set the project directory structure,
which is up to developers.
Poetry can initialize the project in current directory,
which only creates `pyproject.toml`.

```sh
poetry init  # Creates pyproject.toml interactively
```

## Project Dependencies

Similar with `cargo add`, `cargo update`, `cargo remove`.

```sh
poetry add <dependency>
poetry remove <dependency>
poetry update
```

```toml
[tool.poetry.dependencies]
dependency = "^2.1"
```

However, you must manually install dependencies after manually editing `pyproject.toml`.

```sh
poetry install
```

### Operating Modes

Poetry can be operated in two different modes.
The default mode is the package mode, which is the right mode if you want to package your project into an sdist or a wheel and perhaps publish it to a package index.
In this mode, some metadata such as `name` and `version`, which are required for packaging, are mandatory.
Further, the project itself will be installed in editable mode when running `poetry install`.

If you want to use Poetry only for dependency management but not for packaging, you can use the non-package mode:

```toml
[tool.poetry]
package-mode = false
```

In this mode, metadata such as `name` and `version` are optional.
Therefore, it is not possible to build a distribution or publish the project to a package index.
Further, when running poetry install,
Poetry does not try to install the project itself,
but only its dependencies (same as `poetry install --no-root`).

## Project Environment Management

Poetry will first check if it’s currently running inside a virtual environment.
If it is, it will use it directly without creating a new one.
But if it’s not, it will use one that it has already created or create a brand new one for you.

By default, Poetry creates a virtual environment as `{cache-dir}/virtualenvs/{project_name}-{tag}-py{python_version}`.
You can change the `cache-dir` value by editing the Poetry configuration.
Additionally, you can use the `virtualenvs.in-project` configuration variable to create virtual environments within your project directory, which is recommended.

You can create or remove the environment with `env` family commands.
`env use` will create new environment with specified python version.

```sh
poetry env use /full/path/to/python
poetry env use python-executable-in-PATH
poetry env use 3.minor_ver
```

`env info` shows currently activated environment info,
while `env list` lists all environments associated with the project.

```sh
poetry env info
poetry env list
```

`env remove` will delete existing environment.

```sh
poetry env remove /full/path/to/python
poetry env remove python-executable-in-PATH
poetry env remove 3.minor_ver
poetry env remove project-tag-py3.minor_ver
poetry env remove --all
```

You can run your command within virtual environment by `poetry run`, or activate the environment by `poetry shell`.
`poetry shell` will run the nested shell and activate the environment.

```sh
# Use venv
poetry run <command>
# Activate venv
poetry shell
exit
```
