# Blocks

## Behavioral Blocks

- `initial` block ⇒ executes only once, starts at time 0 if simulation
- `always` block ⇒ executes continuously, starts at time 0 if simulaton
  - Event Control ⇒ `@(condition)`
    - Level sensitive: 값이 변할 때
    - Edge triggered: edge에서
    - `@(posedge clk or rst)` ⇒ clk posedge or rst change
    - `@(*)` menas any value change, so identical to comb circuit

## Generate Blocks

```verilog
genvar i; // generation variable
generate for (i = 0; i < N; i = i+1) begin:blockname
    wire a, b;
    and myand1 (a, in_a[i], in_b[i]);
    and myand2 (b, in_c[i], in_d[i]);
    or myor (out[i], a, b);
endgenerate

// is equivalent to

wire blockname[0].a, blockname[0].b;
and blockname[0].myand1 (blockname[0].a, in_a[0], in_b[0]);
and blockname[0].myand2 (blockname[0].b, in_c[0], in_d[0]);
or blockname[0].myor (out[0], blockname[0].a, blockname[0].b);
// and continues...
```

- if/else와 함께 쓸 수 있다. #ifdef같은 느낌으로
