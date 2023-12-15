# Test Metric

## How Much Testing?

- Code-to-test ratio
- Coverage

## How Much Quality?

- ABC score
- Cyclomatic complexity

## Coverage Types

- Statement Coverage (lines of code)
- Branch Coverage (Condition coverage, 2(T/F) per each if-else branch)
- Path Coverage (# of paths branched by if-else)

## ABC Score

- A = # of assignment
- B = # of branch(jump, usually function calls)
- C = # of conditions
- RMS of A, B, C

## Cyclomatic Complexity

- Construct control-flow graph
- E = # of edge
- N = # of node
- P = # of connected components
- CC = E - N + 2P (in most simple case, P = 1, N = E + 1 ⇒ CC = 1)

## Testing Methods

- Equivalence Testing
    - Groups of inputs whose expected output is equivalent
    - Only one input per group is enough
- Boundary Testing
    - Focus on edge case
- Control-flow Testing (White box)
    - Ensures Condition Coverage
    - Ensures Path Coverage
- State-based Testing (White box)

## Test-Driven Development (TDD)

- Write test first (RED)
- Code that pass test (GREEN)
- Refactor the code (Refactor)
- Effective to test logic / behavior
