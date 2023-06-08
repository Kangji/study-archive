# Coq

Coq is a formal proof management system.
It provides a formal language to write mathematical definitions,
executable algorithms and theorems together with an environment for semi-interactive development of machine-checked proofs.

## [Install Coq](https://coq.inria.fr/download)

After installing coq, setup `PATH` variable point installed binaries (coqc, coqtop).

For example (macOS), if you installed coq from binary,
you will find the `.app` in your Applications directory:
`/Applications/Coq-Platform~<version>~<month>.app/Contents/Resources/bin`.

## VsCoq Extension

Install VsCoq extension from marketplace.

Useful commands:

* About: explain the query.
* Check: check the type of query.
* Reset: Reset the focus to the start of the file.
* Interpret to End: Interpret to the end of the file.
* Interpret to Point: Interpret to the current point(cursor)
* Move cursor to the current focus(interpretation point)
* Step Forward: interpret next line.
* Step Backward: return to previous line.

## My Custom Shortcut Settings

* About: ctrl + cmd + a (default)
* Check: ctrl + cmd + c (default)
* Reset: ctrl + cmd + home (default)
* Interpret to End: ctrl + alt + end chord to ctrl + alt + end
* Interpret to Point: ctrl + alt + end
* Move cursor to the current focus: ctrl + alt + . (default)
* Step Forward: ctrl + alt + pagedown
* Step Backward: ctrl + alt + pageup

## Reference Manual

This directory contains the summary of [official reference manual](https://coq.inria.fr/refman/).

### Language Specification

* [Basic Notations and Conventions](/coq/language_spec/00_basic.md)
* [Sorts](/coq/language_spec/01_sort.md)
* [Functions and Assumptions](/coq/language_spec/02_functions.md)
* [Definitions](/coq/language_spec/03_definition.md)
* [Conversion Rules](/coq/language_spec/04_conversion.md)
* [Typing](/coq/language_spec/05_typing.md)
* [Pattern Matching](/coq/language_spec/06_match.md)
* [Inductive Types](/coq/language_spec/07_induction.md)

### Language Extension

* [Existential Variables](/coq/language_extension/00_existential.md)
* [Implicit Arguments](/coq/language_extension/01_implicit.md)

### Proof

* [Proof Mode](/coq/proof/00_proof_mode.md)
* [Tactics](/coq/proof/01_tactics.md)
* [Tacticals](/coq/proof/02_tacticals.md)
