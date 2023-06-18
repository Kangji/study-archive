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

* [Basic Notations and Conventions](language_spec/00_basic.md)
* [Sorts](language_spec/01_sort.md)
* [Functions and Assumptions](language_spec/02_functions.md)
* [Definitions](language_spec/03_definition.md)
* [Conversion Rules](language_spec/04_conversion.md)
* [Typing](language_spec/05_typing.md)
* [Pattern Matching](language_spec/06_match.md)
* [Inductive Types](language_spec/07_induction.md)

### Language Extension

* [Existential Variables](language_extension/00_existential.md)
* [Implicit Arguments](language_extension/01_implicit.md)

### Proof

* [Proof Mode](proof/00_proof_mode.md)
* [Tactics](proof/01_tactics.md)
* [Tacticals](proof/02_tacticals.md)

## Software Foundations

The Software Foundations series is a broad introduction to the mathematical underpinnings of reliable software.

The principal novelty of the series is that every detail is one hundred percent formalized and machine-checked:
the entire text of each volume, including the exercises, is literally a "proof script" for the Coq proof assistant.

The [linked repository](https://github.com/Kangji/SF2023S) contains the code covered by @Kangji in SNU Software Foundations 2023 Spring.

* [Constructive Logic](software_foundations/01_logic.md)
* [Inductively Defined Propositions](software_foundations/02_indprop.md)
* [Simple Imperative Programs](software_foundations/03_imp.md)
* [Hoare Logic](software_foundations/04_hoare.md)
* [Smallstep Operational Semantics](software_foundations/05_smallstep.md)
* [Type System](software_foundations/06_types.md)
* [Simply Typed Lambda Calculus](software_foundations/07_stlc.md)
