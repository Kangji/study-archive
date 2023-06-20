# Basics Components

## Number Representation

`[-][<size>]['<base>]<number>`

- sign ⇒ b’s complement
- size defaut = 32
- base default = d (decimal)
  - b: bin, h: hex, o: oct
- special digit: x(conflict) and z(floating) ⇒ unknown

## Data Types

- wire(or net), reg
- integer ⇒ singed reg
- array or vector ⇒ [msb:lsb] 똑같은데 다름
  - (array)`reg a [3:0]` or (vector)`reg [3:0] a`
  - (array) `reg a [3:0][3:0][31:0]` or (array of vector) `reg [31:0] a [3:0][3:0]`
  - “vector” concatenation ⇒ {a, b, c}

## Assignment

- `assign <wire> = expr;`
- `<reg vector> = expr;` i.e., `{a, b} = 2'b10;` is `a = 1'b1; b = 1'b0;`
- `<reg vector> <= expr` i.e., `{a, b} <= 2'b10;`

## Operator

- Reduction:  bitwise operation, vector to bit
- Case equality: `===` and `!==`: cares x and z (like `is` in SQL)

## Loop

- for and while (identical to C language)
- `repeat(iter_cnt)`: fixed number iteration
