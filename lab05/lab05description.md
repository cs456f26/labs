# Lab05 Register with Load and Reset in an UpCounter

This lab incorporates what you have learned with combinational and sequential circuits to create counters
with the experience and reading on Verilog to create a 4-bit upcounter with a load and reset. Behavioral
Verilog can be used to make the 4-bit register, the 4-bit 2-1 multiplexor, and a 4-bit adder. All of
these should be used in the top module and connected using structural Verilog.

You will not need to use the boards today.

## Submission

Submit a pdf with the following schematics and related timing diagrams
with appropriate formatting as in past labs. (Names, assignment, section titles etc.)

More specifically:
1) (4 pts) Verilog code, schematic and timing diagram for a 4-bit register with load and reset
2) (4 pts) Verilog code, schematic and timing diagram for the upcounter using the provided testbench
3) (2 pts) Verilog code for all the other submodules used

## Block diagram of desired circuit

Use the name `lab06_upcounter` for this project.

![upcounter](upcounter4bit_ld_rst.png)

## More details about verilog file names etc.

You should have the following modules in today's design:
1) a 4-bit register with load and reset: `reg_4bit_ldrst`, using code below,
2) a 4-bit 2-to-1 bit multiplexor: `mux_4bit_2_1`,
3) a 4-bit adder: `add_4bit`, and
4) a top module with the 4-bit upcounter with load and reset: `upcounter_4bit_ldrst_top`.

For the first set of results, you should write and use your own testbench for the `reg_4bit_ldrst` module
to test appropriate conditions to create a reasonable timing diagram (aka waveform). You can modify the
provided testbench for the upcounter if you wish.

For the second set of results to turn in you should use the provided testbench below to test the
`upcounter_4bit_ldrst_top` module and create the schematic. Look at the testbench when designing your module 
to make sure that you are using the same order and input format for the module as it is used in the testbench.

Please make sure and include the Verilog code for each of the modules 1-4 above. While it is wise to create
testbenches for each, you are not required for this lab to turn in testbenches.

## Getting started on this lab

Note that the project name should be `lab06_upcounter`. You may need to look back at previous 
labs to recall the details on how to get set up. Start with making the 4-bit register using the code below.
Notice that this code intentionally creates a latch. For each module you should check the schematic before
moving forward to make sure that your code has "drawn" the intended circuit.

```verilog
`timescale 1 ns/ 1 ns
module reg_4bit_ldrst(
    input clk,
    input rst,
    input ld,
    [3:0] Lvalue,
    output reg [3:0] out
);
    always @(posedge clk) begin
        if (rst) begin
            out <= 4'b0;
        end else if (ld) begin
            out <= Lvalue;
        end
    end
endmodule
```

## Provided testbench

```verilog
`timescale 1 ns/ 1 ns 

module upcounter_4bit_ldrst_top_tb;
    reg clock, clear, count, load;
    reg [3:0] Lvalue;
    wire [3:0] Count;    
       
    localparam time_step = 10;
    upcounter_4bit_ldrst_top upcounter_4bit_ldrst_top_tb(
        clock, clear, count, load, Lvalue, Count);
    
    initial
        begin           
            clock = 0;
            clear = 0;
            count = 0;
            load = 1;
            Lvalue = 4'd2;  // start with non-zero to see diff from reset
            #time_step;
            
            clock = 1;
            #time_step;
                      
            clock = 0;
            clear = 0;
            count = 1;
            load = 0;
            #time_step;
                                              
            clock = 1;
            #time_step;
                        
            clock = 0;
            #time_step;
                 
            clock = 1;
            #time_step;
                       
            clock = 0;
            #time_step;
                 
            clock = 1;
            #time_step;

            clock = 0;
            clear = 1;
            count = 0;
            load = 0;
            #time_step;

            clock = 1;
            #time_step;
            $finish();         
        end
    
endmodule
```
