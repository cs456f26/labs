# Lab04 Circuits with Behavioral Verilog
At this point, we have focussed solely on combinational circuits and using structural verilog to create designs.
The goal was to give you a strong foundation in circuit design so that you can understand what the more powerful
behavioral descriptions will do. The ALU project is still combinational logic (there is no clock or feedback loop). 
This lab explains when to use different versions of continuous assignment and demonstrates how using behavioral 
verilog a designer can inadvertently create latches changing the intended design to something completely different. 

## Submission
Submit a pdf with the following schematics and related timing diagrams as well as answering the questions indicated with appropriate formatting as in past labs. (Names, assignment, section titles etc.)

More specifically:
1) (2 pts) schematic and timing diagram for nonblocking assignment in Running an experiment
2) (2 pts) schematic and timing diagram for blocking assignment in Running an experiment
3) (2 pts) blocking.v and blocking_tb.v
4) (0.5 pt) answer question: What is the difference between the two schematics for blocking and non-blocking?
5) (2 pts) Mux schematic with latch and without
6) (0.5 pts) answer question: What did you do to remove the latch in Multiplexors with if else
7) (1 pt) Last two schematics


## Assignment in Verilog
There are three versions of assignment symbols/uses in Verilog.
1. `assign {netname} =` outside of an always @ block
2. `=` within an always @ or initial begin block
3. `<=` usually used within an always @ block

### assign =
`assign =` is used outside of an always @ or initial block to create a `continuous assignment` of the right hand
side of the equals symbol to the left hand side of the equals symbol. The left hand side must be a reg or a wire net
and the right hand side must be an expression in verilog that can be synthesized (i.e. it can be implemented as a circuit).
Continuous assignment is used to specify combinational circuits where the right hand side is continuously assigned or
connected to the left hand side anytime there is a change in any signal on the right hand side.

### = within an always or initial block
`=` alone must be within either an always @ block/procedure or within an initial block. In this class initial blocks
are used pretty much exclusively in a test bench. `=` is known as a `blocking` assignment. It means that the assignments
will happen sequentially within a block. `=` within an always block specifies a combinational circuit and the
sensitivity list must include all input signals. Best practice is to use `*` for the sensitivity list. 
`=` used within an initial block in a testbench is forcing a signal value for a period of time until it is 
specifically changed by another `=` assignment.

### <= with an always block
`<=` within an always block is a `non-blocking` assignment. It is used to specify a sequential circuit and the 
sensitivity list should designate an edge of the clock. With non-blocking assignment all of the right hand sides
of the assignment statements are evaluated and then immediately assigned to the left hand side in parallel.

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
        end
    
endmodule

```
Now create a module called blocking by starting with the code above and making the following changes: a) change the always @ sensitivity list to only include `in` and b) use the `=` blocking assignment. Then create a testbench module called  blocking_tb that no longer uses the clock and instead, just cycles `a`. See how this is different in terms of the schematic and the timing diagram and explain that difference in your write-up for the lab briefly.

## Multiplexors with if-else
Note that a multiplexor is a combinational circuit. To specify one in behavioral verilog the simplest way is to use an if-else statement or a case statement. It is easy to introduce a latch 
unintentionally using these constructs by not assigning a value to a register for every possible 
condition, similar to what is done in the code below. Use the code below to create a schematic in 
Vivado. 

```verilog
module simple_mux(input [1:0] x, output reg y);
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

Finally, there are verilog control constructs that cannot be synthesized. Specifically, `for` and `while` 
loops cannot be mapped to hardware. Rather they are used to be able to specify multiple instantiations 
of modules or in a testbench to loop through a series of tests.

The following two code examples with corresponding test benches are other examples for you to explore. Capture the schematic and the timing diagram for each for your lab report if you have time.

## Always_block_no_clock
---

### Code

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
Create a new file using the testbench below to simulate the verilog code above.

```verilog

module always_no_clock;


    reg a;
    wire b_out, c_out, d_out;
    
       
    localparam time_step = 5;
    always_not_block_sense always_not_block_sense_tb(.a_in(a),  .b_out(b_out), .c_out(c_out), .d_out(d_out));
    
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
           
           
        end
    
endmodule

```


----

## Always_block_clock
Use the verilog code and the associated testbench below to create a schematic and timing diagram using a clock and an always block. Note the differences between the two schematics with and without a clock.

### Code

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

```verilog
module always_clock;

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
                       
                       
           
           
        end
    
    
endmodule

```

