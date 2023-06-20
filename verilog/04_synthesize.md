# Synthesizable Code

- Modulus, exponent, arithmetic shift, case equality are non-synthesizable
- Incomplete branches are non-synthesizable
- Mixed edge/level signal are non-synthesizable ⇒ do not use

## Latch Inference

- incomplete if-else statement (mux)
  - also implies the statement with redundant branch (i.e., `y = y`)
  - add extra level (latch)
- Two ways to avoid latch
  - initialize reg
  - complete branch
