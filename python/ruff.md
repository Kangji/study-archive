# Ruff

An extremely fast Python linter and code formatter, written in Rust.
As can be inferred from being made in Rust, its usage is very similar to cargo.

## Installation

```sh
pip install ruff
```

## Configuration

Ruff can be configured through a `pyproject.toml`, `ruff.toml`, or `.ruff.toml` file.
Whether you're using Ruff as a linter, formatter, or both, the underlying configuration strategy and semantics are the same.

If left unspecified, Ruff's default configuration is equivalent to:

```toml
# Exclude a variety of commonly ignored directories.
exclude = [
    ".bzr",
    ".direnv",
    ".eggs",
    ".git",
    ".git-rewrite",
    ".hg",
    ".ipynb_checkpoints",
    ".mypy_cache",
    ".nox",
    ".pants.d",
    ".pyenv",
    ".pytest_cache",
    ".pytype",
    ".ruff_cache",
    ".svn",
    ".tox",
    ".venv",
    ".vscode",
    "__pypackages__",
    "_build",
    "buck-out",
    "build",
    "dist",
    "node_modules",
    "site-packages",
    "venv",
]

# Same as Black.
line-length = 88
indent-width = 4

# Assume Python 3.8
target-version = "py310"

[lint]
# Enable Pyflakes (`F`) and a subset of the pycodestyle (`E`)  codes by default.
# Unlike Flake8, Ruff doesn't enable pycodestyle warnings (`W`) or
# McCabe complexity (`C901`) by default.
select = ["E4", "E7", "E9", "F"]
ignore = []

# Allow fix for all enabled rules (when `--fix`) is provided.
fixable = ["ALL"]
unfixable = []

# Allow unused variables when underscore-prefixed.
dummy-variable-rgx = "^(_+|(_+[a-zA-Z0-9_]*[a-zA-Z0-9]+?))$"

[format]
# Like Black, use double quotes for strings.
quote-style = "double"

# Like Black, indent with spaces, rather than tabs.
indent-style = "space"

# Like Black, respect magic trailing commas.
skip-magic-trailing-comma = false

# Like Black, automatically detect the appropriate line ending.
line-ending = "auto"

# Enable auto-formatting of code examples in docstrings. Markdown,
# reStructuredText code/literal blocks and doctests are all supported.
#
# This is currently disabled by default, but it is planned for this
# to be opt-out in the future.
docstring-code-format = false

# Set the line length limit used when formatting code snippets in
# docstrings.
#
# This only has an effect when the `docstring-code-format` setting is
# enabled.
docstring-code-line-length = "dynamic"
```

### Config File Discovery

Similar to `ESLint`, Ruff supports hierarchical configuration,
such that the "closest" config file in the directory hierarchy is used for every individual file,
with all paths in the config file (e.g., exclude globs, src paths) being resolved relative to the directory containing that config file.

There are a few exceptions to these rules:
1. In locating the "closest" `pyproject.toml` file for a given path,
Ruff ignores any `pyproject.toml` files that lack a [tool.ruff] section.
2. If a configuration file is passed directly via `--config`, those settings are used for all analyzed files, and any relative paths in that configuration file (like exclude globs or src paths) are resolved relative to the current working directory.
3. If no config file is found in the filesystem hierarchy, Ruff will fall back to using a default configuration. If a user-specific configuration file exists at ${config_dir}/ruff/pyproject.toml, that file will be used instead of the default configuration, with ${config_dir} being determined via the dirs crate, and all relative paths being again resolved relative to the current working directory.
4. Any config-file-supported settings that are provided on the command-line (e.g., via --select) will override the settings in every resolved configuration file.

## Linter: `ruff check`

```sh
ruff check                  # Lint all files in the current directory.
ruff check --fix            # Lint all files in the current directory, and fix any fixable errors.
ruff check --watch          # Lint all files in the current directory, and re-lint on change.
ruff check path/to/code/    # Lint all files in `path/to/code` (and any subdirectories).
```

Please see `ruff check --help` for full docs.

### Rule Selection

Rule selection is enabled by configuration, yet can be overwritten by command.

```sh
ruff check --select F401        # Lint by enforcing F401 only.
ruff check --extend-select B    # Lint by enforcing B also.
```

### Fix Safety

Ruff labels fixes as "safe" and "unsafe".
The meaning and intent of your code will be retained when applying safe fixes,
but the meaning could be changed when applying unsafe fixes.

```sh
ruff check --unsafe-fixes   # Include unsafe fixes.
```

### Exit Code

By default, ruff will exit with non-zero when lint violations remain after optionally applying fixes.

```sh
ruff check [-e | --exit-zero]       # Exit with 0 even upon detecting violations.
ruff check --exit-non-zero-on-fix   # Exit with non-zero if any files were modified via fix, even if no lint violations remain
```

## Formatter: `ruff format`

```sh
ruff format                   # Format all files in the current directory.
ruff format path/to/code/     # Lint all files in `path/to/code` (and any subdirectories).
ruff format path/to/file.py   # Format a single file.
```
