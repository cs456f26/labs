# Lab04 - Circuits with Behavioral Verilog (lab04_behavioral)
At this point, we have focussed solely on combinational circuits and using structural verilog to create designs.
The goal was to give you a strong foundation in circuit design so that you can understand what the more powerful
behavioral descriptions will do. The ALU project is still combinational logic (there is no clock or feedback loop). 
This lab explains when to use different versions of continuous assignment and demonstrates how using behavioral 
verilog a designer can inadvertently create latches changing the intended design to something completely different.

You will not need the board today.

Important Notes:
1. Name your files as specified in the lab directions for best results.
2. First thing add your name, date, assignment number etc. in the comment block at the beginning.
   This is the first thing that makes it clear it is your code. Also helps with grading.
3. Take screenshots as you go and save them to a properly labeled folder.
4. It may be easier to turn in the verilog as a screen capture also to maintain the legibility and formatting.
5. This lab does not use the board, so you do not need a constraints file.

# Submission Details
Today's lab will be a pdf report submitted to Canvas with the following schematics and related timing diagrams as
well as answers to the questions indicated, with appropriate formatting as in past labs (names, assignment, section
titles etc.).

Today's lab will be graded as follows:
1. (2 pts) Schematic and timing diagram for `nonblocking` in Running an experiment.
2. (2 pts) Schematic and timing diagram for `blocking` in Running an experiment.
3. (1 pt) `blocking.v` and `blocking_tb.v`.
4. (1.5 pts) Answer the question: What is the difference between the two schematics for blocking and non-blocking?
5. (1 pt) `simple_mux` schematic with the latch and without it.
6. (1.5 pts) Answer the question: What did you do to remove the latch in Multiplexors with if-else?
7. (1 pt) Schematics and timing diagrams for `always_block_sense` and `always_block`, and a sentence on the
   difference between the two schematics.

# Learning outcomes
1. Understanding the three kinds of assignment in Verilog and when to use each.
2. Seeing how blocking and non-blocking assignment produce different circuits.
3. Recognizing and removing unintended latches in behavioral Verilog.

## Project creation
1. Use the `Windows` key to bring up the search bar for `Vivado`.
2. Start `Vivado`.
3. Under Quick Start, choose Create Project.
4. Hit Next to use the assist at creating projects Wizard.
5. Fill in the project name `lab04_behavioral` and file location.
6. Default is rtl project, which is what you want so just click Next.
7. Don't create a new file or add a constraints file, just click Next.
8. Select the `Board` tab. Under `Name` find PYNQ-Z1.
9. Select PYNQ-Z1 in table below. Make sure that the Part is xc7z020clg400-1. Choosing the wrong part causes
   missing package pin errors in later labs when you reuse this setup with the board.

Each module below goes in its own design source file named after the module (for example `nonblocking.v`), and each
testbench goes in its own simulation source named after the module with `_tb` added (for example `nonblocking_tb.v`).
View schematics with `RTL Analysis` -> `Open Elaborated Design` and timing diagrams with `Run Simulation`.

## Assignment in Verilog
There are three versions of assignment symbols/uses in Verilog. 
Note that a netname is a wire or connection between parts in a circuit.
1. `assign {netname} =` outside of an always @ block
2. `=` within an always @ or initial begin block
3. `<=` usually used within an always @ block

### assign =
`assign =` is used outside of an always @ or initial block to create a `continuous assignment` of the right hand
side of the equals symbol to the left hand side of the equals symbol. The left hand side must be a wire net (not a reg)
and the right hand side must be an expression in verilog that can be synthesized (i.e. it can be implemented as a
circuit).
Continuous assignment is used to specify combinational circuits where the right hand side is continuously assigned or
connected to the left hand side anytime there is a change in any signal on the right hand side.

### = within an always or initial block
`=` alone must be within either an always @ block/procedure or within an initial block. In this class initial blocks
are used pretty much exclusively in a test bench. `=` is known as a `blocking` assignment. It means that the assignments
will happen sequentially within a block. `=` within an always block typically specifies a combinational circuit, and
in that case the sensitivity list must include all input signals. Best practice is to use `*` for the sensitivity list. 
`=` used within an initial block in a testbench is forcing a signal value for a period of time until it is 
specifically changed by another `=` assignment.

### <= with an always block
`<=` within an always block is a `non-blocking` assignment. It is used to specify a sequential circuit and the 
sensitivity list should designate an edge of the clock. With non-blocking assignment all of the right hand sides
of the assignment statements are evaluated first, and only then are the left hand sides updated, all together at the
end of the time step. This is why the assignments behave as if they happen in parallel.

## Running an experiment
Use the verilog code and testbench start below to run an experiment. First use the code below using a non-blocking assignment.
View and capture the schematic and use the testbench to create a timing diagram to show the basic functionality.

```verilog
module nonblocking (
    input in, clk,
    output reg out
    );
          
    reg q1, q2;
    always @(posedge clk) begin
      q1 <= in;
      q2 <= q1;
      out <= q2;
    end
endmodule

```
```verilog
`timescale 1 ns/ 1 ns

module nonblocking_tb;
    reg a, clock;
    wire out;
    
       
    localparam time_step = 10;
    nonblocking nonblocking_tb(a, clock, out);
    
    initial
        begin           
            clock = 0;
            a = 0;
            #time_step;
            
            clock = 1;
            a = 1;
            #time_step;
                      
            clock = 0;
            a = 1;
            #time_step;
                                              
            clock = 1;
            a = 0;
            #time_step;
                        
            clock = 0;
            a = 0;
            #time_step;
                 
            clock = 1;
            a = 1;
            #time_step;
                       
            clock = 0;
            a = 0;
            #time_step;
                 
            clock = 1;
            a = 1;
            #time_step;
            $finish();
        end
    
endmodule

```
Now create a module called `blocking` in `blocking.v` by starting with the code above and making the following changes: a) change the always @ sensitivity list to only include `in` and b) use the `=` blocking assignment. Then create a testbench module called `blocking_tb` in `blocking_tb.v` that no longer uses the clock and instead, just cycles `a`. See how this is different in terms of the schematic and the timing diagram and explain that difference in your write-up for the lab briefly.

## Multiplexors with if-else
Note that a multiplexor is a combinational circuit. To specify one in behavioral verilog the simplest way is to use an if-else statement or a case statement. It is easy to introduce a latch 
unintentionally using these constructs by not assigning a value to a register for every possible 
condition, similar to what is done in the code below. Use the code below in `simple_mux.v` to create a
schematic in Vivado. 

```verilog
module simple_mux(input [1:0] x, output reg [1:0] y);
    always @(*) begin
        if (x == 2'b10) begin
            y = 2'd3;
        end else if(x == 2'b11) begin
            y = 2'd2;
        end
    end
endmodule
```
Capture that schematic and then change the code to make sure all possibilities of `x` are included
in the always block by including a default value for y of zero. Capture this second schematic. Explain in the write-up what you did to remove the latch.

Note that in addition to using case or if-else statements, a ternary operator also exists that
can be used to specify a multiplexor. For example, here is an expression that implements min(a, 10) 
`assign out = a > 10 ? 10 : a;` a one-bit multiplexor with inputs `a` and `b` and select `s` would
be `assign out = s == 0 ? a : b;`

Finally, loops in verilog do not become loops in hardware. A `for` loop with fixed bounds can be synthesized, but
the tool unrolls it into repeated copies of the hardware; `while` loops are generally not synthesizable. Loops are
mostly used inside `generate` blocks to specify multiple instantiations of modules, or in a testbench to loop
through a series of tests.

The following two code examples with corresponding test benches are other examples for you to explore. Capture the schematic and the timing diagram for each for your lab report.

## Always block without a clock (always_block_sense)

### Code
Put this module in `always_block_sense.v`.

```verilog
module always_block_sense(
    input a_in, 
    output b_out,
    output c_out,
    output d_out
    );

    reg B;
    reg C;
    reg D;
    
    always @ (a_in) begin
    B = a_in;
    C = B;
    D = C;
    end
    
    
    assign b_out = B;
    assign c_out = C;
    assign d_out = D;

endmodule

```
### TestBench
Create a new simulation source `always_block_sense_tb.v` using the testbench below to simulate the verilog code above.

```verilog
`timescale 1 ns/ 1 ns

module always_block_sense_tb;


    reg a;
    wire b_out, c_out, d_out;
    
       
    localparam time_step = 5;
    always_block_sense always_block_sense_tb(.a_in(a),  .b_out(b_out), .c_out(c_out), .d_out(d_out));
    
    initial
        begin
           
            
            a = 0;
            #time_step;
            
            a = 1;
            #time_step;     
            
            
            a = 1;
            #time_step;
                                
            
            a = 0;
            #time_step;
            
            a = 1;
            #time_step;
                       
                       
            a = 0;
            #time_step;
            $finish();
        end
    
endmodule

```


## Always block with a clock (always_block)
Use the verilog code and the associated testbench below to create a schematic and timing diagram using a clock and an always block. In your write-up, briefly describe the difference between the two schematics with and without a clock.

### Code
Put this module in `always_block.v`.

```verilog
module always_block(
        input clock,
        input a_in, 
        output b_out,
        output c_out,
        output d_out
    );
    
    reg B;
    reg C;
    reg D;

    always @ ( posedge clock ) begin
        B = a_in;
        C = B;
        D = C;
    end
    
    
    assign b_out = B;
    assign c_out = C;
    assign d_out = D;
endmodule

```

### TestBench
Create a new simulation source `always_block_tb.v` using the testbench below.

```verilog
`timescale 1 ns/ 1 ns

module always_block_tb;

    reg a, clock;
    wire b_out, c_out, d_out;
    
       
    localparam time_step = 5;
    always_block always_block_tb(.clock(clock), .a_in(a),  .b_out(b_out), .c_out(c_out), .d_out(d_out));
    

    
    initial
        begin
           
            clock = 0;
            a = 0;
            #time_step;
            
            clock = 1;
            a = 1;
            #time_step;
            
            
            clock = 0;
            a = 1;
            #time_step;
                       
                       
            clock = 1;
            a = 0;
            #time_step;
            
            
            clock = 0;
            a = 0;
            #time_step;
            
            
            clock = 1;
            a = 1;
            #time_step;
            $finish();
        end
    
    
endmodule

```

