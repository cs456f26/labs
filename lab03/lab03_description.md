# Lab03 - Structural verilog for combinational circuits and testbench use (lab03_muxadd)
Important Notes: This may take a bit longer than the class period.
1. Name your files as specified in the lab directions for best results.
2. First thing add your name, date, assignment number etc. in the comment block at the beginning.
   This is the first thing that makes it clear it is your code. Also helps with grading.
3. Take screenshots as you go and save them to a properly labeled folder.
4. It may be easier to turn in the verilog as a screen capture also to maintain the legibility and formatting.
5. This lab uses the board so you need a constraints file. Comment out all of the
   lines in the constraints file at first, until you are running the top file to
   avoid warnings regarding the hardware ports.

# Submission Details
Today's lab will be a pdf report submitted to Canvas. The goal is to build a 2-1 multiplexor and 1-bit full 
adder to use in a provided top design and make sure it works on the board. You will submit your verilog, a schematic,
a timing diagram for the mux and adder, a timing diagram for the top design, and two pictures of your board 
with a person pressing the buttons and showing the correct output on the LEDS for one 
set of inputs with SWITCH[0] = 0 to choose the adder and another with SWITCH[0] = 1 to 
choose the and gate. 

Today's lab will be graded as follows:
1. (0 pts) Formatting with team names on top right hand side with title of assignment immediately underneath
   and all right justified. Each picture clearly labeled. Will subtract points if needed.
2. (3 pts) Structural verilog, schematic, and timing diagram with provided testbench for a 2-1 multiplexor.
3. (3 pts) Structural verilog, schematic, and timing diagram with your testbench for a 1-bit adder.
4. (2 pts) Timing diagram using lab03_muxadd_top and provided testbench.
5. (2 pts) Two pictures of correct board output a) with SWITCHES[0] on and b) with SWITCHES[0] off.
   
# Learning outcomes
1. Increasing familiarity with Vivado project creation, simulation, and downloading to the board.
2. Reinforcement of basic circuit design using gates and creating modules to use in larger design.
3. Experience with using a testbench to test circuits and generate timing diagrams.

## Project creation
Start the project
1. Use the `Windows` key to bring up the search bar for `Vivado`.
2. Start `Vivado`.
3. Under Quick Start, choose Create Project.
4. Hit Next to use the assist at creating projects Wizard.
5. Fill in the project name `lab3_muxadd` and file location.
6. Default is rtl project, which is what you want so just click Next.
7. Don't create a new file yet, just click Next.
8. Include the constraint file from lab02.
9. Select the `Board` tab. Under `Name` find PYNQ-Z1.
10. Select PYNQ-Z1 in table below. Make sure that the Part is xc7z020clg400-1.

# Individual Designs

## Editing file
1. In the middle Sources window choose the plus tab.
2. Choose add or create design sources. Click `Next`.
4. Create new file by choosing the plus tab again, -> Create File. Name it `mux2_1`.
5. In the future you can specify parameters to your Module here, but for now lets make sure 
   to learn the whole structure of a Verilog program so you can write it yourself. Just click OK.
6. Click Yes.
7. Now you will notice in the Sources Window under Design Sources that the `mux2-1` file has been created.
8. Double click on the file and it will open a window on the right side.
9. Fill in the team member names under Engineer. As well as the other pertinent information.

-------
## Write Verilog for a 2-1 multiplexor y = d0s' + d1s
1. Write structural verilog for the boolean logic expression `y = d0s' + d1s`. File name should be `mux2_1`.
   To write the structural verilog, use wires to connect gates as outlined in the example below for a module
   to implement a 3-input or.
```verilog
module or_3in (input i1, input i2, input i3, output out);
    wire or_out;
    or(or_out, i1, i2);
    or(out, or_out, i3);
endmodule
```
3. After writing verilog for your multiplexor, create the 'schematic' and verify it is the correct circuit.
   Recall `RTL analysis` -> `Open Elaborated Design`
5. Edit your verilog if necessary.
6. Once your verilog and circuit are correct get screen captures for your report.
7. In a prior lab, force values were used to create a timing diagram that demonstrates the circuit is functioning correctly.
    Generating these force values over and over is time consuming and has to be repeated each debugging session.
   Today we will learn to use a testbench to run a simulation instead.

## Understanding a testbench ##
6. A testbench is a way to run a simulation without typing in tcl commands. Below is one designed to test all inputs of the `mux2_1` module you wrote above. Let's make sure you understand key aspects of the code below.
   
### Timescale
```verilog
`timescale 1 ns/ 1 ns
```
The first unit in the timescale indicates the reference time scale units of delay and the second unit indicates the precision.
We will generally just use 1 ns for both.

### Circuit Under Test
Note the name of the module is the same as the CUT (Circuit Under Test) followed by _tb. This is not required, but is
convention and highly recommended. Often all signals in the testbench are also followed by _tb, but is omitted here for
simplicity. Once the timescale is set the command `#`number can be used to have the simulation delay changes for number time
units.
### Registers
`reg d0;` or "register d0" becomes a variable we can set to hold a signal. Registers are needed for inputs into the
instantiation of a module being tested.
### Wires
`wire out;` is literally a wire in a circuit. Wires are needed for outputs from the module being tested.
### Constants
`localparam` is used to define a constant name for more readable and maintainable code. Now when we delay the simulation for a certain number of time units, and want to change it later, we only have to change it in one place.
### Instantiating the circuit under test
`mux2_1 mux2_1_tb(d0, d1, s, out);` instantiates a mux2_1 module "type" that is named mux2_1_tb much like in Java you 
instantiate an object. In this case instead of passing parameter values to a constructor, you are connecting signals in the 
test module to the inputs and outputs of the circuit under test.
### Setting signals
Each assignment to a register within the initial block happens in the order given. Though "time" in the simulation does not
pass until the `#` directive is used.

**Note the lines before the initial begin are setting up the circuit. Those within the initial clause are for running the simulation.**

```verilog
`timescale 1 ns/ 1 ns

module mux_tb;
    reg d0;
    reg d1;
    reg s;
    wire out;
          
    localparam time_step = 5;

    mux2_1 mux2_1_tb(d0, d1, s, out);
    
    initial
        begin   
            d0 = 0;
            d1 = 0;
            s = 0;  //000
            #time_step;
            
            d0 = 1;  //001
            #time_step;                 
            
            d0 = 0;
            d1 = 1;   //010
            #time_step;
                                
            d0 = 1;   //011
            #time_step;

            s = 1;   //111
            #time_step;

            d0 = 0;  //110
            #time_step;

            d1 = 0;   //100
            #time_step;

            d0 =  1; //101
            #time_step;
            $finish();
    end
endmodule

```
7. Create a new file by using the `+` under Source files and add a simulation source. Copy the testbench code above designed to simulate the verilog module `mux2_1`.
   
## Simulate ##
8. Under the Flow Navigator, use `Run Simulation` to run the code in the testbench. Once you have inspected the timing diagram
to verify the correct output for `y` for all possible inputs of `d0`, `d1` and `s`, create a screen capture of the timing
diagram for your lab report.

___

# Full Adder
---

## Full adder behavior 
A full adder circuit is the basic fundamental circuit that can perform addition or subtraction. It adds together two binary digits and a `carry_in` bit to produce the `sum` and `carry_out` digit. For example, 8 Full adders can be connected in series to make an 8 bit adder that will add two 8-bit numbers in either unsigned binary or two's complement representation.

A one-bit full adder will take in 3 one-bit inputs: two 1-bit inputs representing the digits to add and a one 1-bit `carry_in`. The full adder will have two outputs: a 1-bit `sum` and 1-bit `carry_out` that when used in conjunction with other one-bit adders will be the `carry_in` to the next more significant bit of the addition. 

## Write Verilog for the one-bit full adder
1. Write structural verilog for the one bit full adder. Name this module `full_adder` and saved to `lab03_muxadd`.
2. Create the adder 'schematic' and verify it is the correct circuit. Recall `RTL analysis` -> `Open Elaborated Design`
3. Edit your verilog if necessary.
4. Once your verilog and circuit are correct get screen captures for your report.
5. Using the template for a testbench shown below, create a testbench for your full_adder and run a simulation to show the adder working for all possible inputs.
   
```verilog
`timescale 1 ns/ 1 ns

module full_adder_tb;
    reg a;
    reg b;
    reg c_in;
    wire sum;
    wire c_out;
          
    localparam time_step = 5;

    full_adder full_adder_tb(a, b, c_in, sum, c_out);
    
    initial
        begin   

// Enter your simulation statements here.

        $finish();  
        end
    
endmodule
```
6. Run a simulation and create a screen capture of the timing diagram for your lab report.

## Incorporate top module and testbench to use your multiplexor and adder in a larger design

Now that you have a working multiplexor and a working adder, you can use them to make a larger design.
This time the top file is provided for you along with a testbench. Let's take a look at the top module them 
before incorporating it into your project.

### Top module
The top module has the inputs and outputs that connect this module to the board using the names in the
constraints file like in lab02. It also "instantiates" a full adder and a multiplexor similar to a testbench so
that these modules can be connected together. The overall function of this circuit is to reflect the sum of
the two input buttons if SWITCHES[0] is 0 and to reflect the and of the two input buttons if SWITCHES[0] is 1.

```verilog
module lab03_muxadd_top (
    input [1:0] BUTTONS,
    input [0:0] SWITCHES,
    output [1:0] LEDS
    );
    wire add_out;
    wire and_out;
    full_adder fa(BUTTONS[0], BUTTONS[1], 0, add_out, LEDS[1]);
    and(and_out, BUTTONS[0], BUTTONS[1]);
    mux2_1 m2_1(add_out, and_out, SWITCHES[0], LEDS[0]);
    
endmodule

```
### Top Module Testbench

```verilog
`timescale 1 ns/ 1 ns

module lab03_muxadd_top_tb;
    reg [1:0] b;
    reg s0;
    wire [1:0]leds;
          
    localparam time_step = 5;

    lab03_muxadd_top lab03_muxadd_top_tb(b, s0, leds);
    
    initial
        begin
            // Need 8 test cases, for three inputs.
            // Four tests each setting of switches 0. 
           
            b[0] = 0;
            b[1] = 0;
            s0 = 0;
            #time_step;

            b[0] = 0;
            b[1] = 0;
            s0 = 1;
            #time_step;

            b[0] = 1;
            b[1] = 0;
            s0 = 0;
            #time_step;
            
            b[0] = 1;
            b[1] = 0;
            s0 = 1;
            #time_step;

            b[0] = 0;
            b[1] = 1;
            s0 = 0;
            #time_step;                

            b[0] = 0;
            b[1] = 1;
            s0 = 1;
            #time_step;

            b[0] = 1;
            b[1] = 1;
            s0 = 0;
            #time_step;

            b[0] = 1;
            b[1] = 1;
            s0 = 1;
            #time_step;                

            $finish();
        end
    
endmodule

```
Add the top module and testbench to your project and run a simulation to make sure both are correct.
Remember you will need to set your top module to be ... well the top. Like when you were switching between
modules in prior labs.

## Programming the board
1. Plug in the board.
2. Choose generate bitstream.
3. Open Target
4. Program Device

## Get screenshots of the board with a single correct output on the LEDS[1:0] for each setting of SWITCHES[0]

____



