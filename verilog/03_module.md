# Module

```verilog
module module_name
#(
    // parameter port declaration
)
(
    // list_of_ports
    in_a, in_b, out_a, out_b, result, clk, rst
);
    // parameter declaration if no parameter pr
    parameter N = 32;

    // port_declarations (undeclare => one bit wire)
    input [N-1:0] in_a, in_b;
    input clk, rst;
    output [N-1:0] out_a, out_b;
    output reg [N-1:0] result;
    
    // internal variable declaration
    wire [2*N-1:0] multi;
    reg ovf;

    // module instantiation
    module_name [#param] [instance_name] (port_connection);

    // dataflow statement (assign)
    assign multi = in_a * in_b;
    assign out_a = in_a;
    assign out_b = in_b;

    // behavioral blocks
    always @(posedge clk or rst) begin
        // blocking and nonblocking statements
        if (rst)
            result <= 0;
        else begin
            {ovf, result} <= multi + result;
        end
    end
endmodule
```

Port list and declaration 한번에 할 수 있음

## Testbench

### Timing Control

- `#delay reg [<]= expr` ⇒ delay then statement
- `reg [<]= #delay expr` ⇒ calculate, delay, assign
