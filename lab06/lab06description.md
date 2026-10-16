# Lab06 Inferred Asynchronous ROMs
An asynchronous memory is a memory block that isn’t governed by a clock. A ROM is a read-only memory. 
The data can be accessed by supplying an address to the ROM; after some time, the ROM will output the 
data stored at that address.  A memory block, in general, can contain as many addresses in which to 
store data as desired.  Every address should contain the same amount of data (bits). The number of 
addresses is called the depth of the memory, while the number of bits stored per address is called the 
width of the memory.

The assignment today is to create an asynchronous ROM containing the machine code of two different RISC-V programs.

## Learning Outcomes
1) Introduce the students to the technique of addressing memory.
2) Create a ROM using behavioral verilog.
3) Practice translating RISC V instructions into machine code

## Submission
Submit a pdf with the following: 
1) (2 pts) verilog for your asynchronous ROM of machine code
2) (2 pts) testbench you are using to create a timing diagram that uses a for loop, one for each program
3) (2 pts) schematic for your asynchronouse ROM for the program indicated below
4) (2 pts) timing diagram for two accesses to the ROM, one from each program, showing the correct output

## A small example
The synthesizer takes the Verilog you write and converts it into a low-level netlist of the structures 
that are actually used on the FPGA. The Verilog describes the functionality of some digital circuit and 
the synthesizer infers what primitives implement the functional description. In this section, we will 
examine the Verilog that allows the synthesizer to infer a ROM. This is a minimal example of a ROM in 
Verilog: (depth of 8 entries/addresses, width of 12 bits).

```verilog
module rom (input [2:0] address, output reg [11:0] data); 
  always @(*) begin
    case(address)
        3'd0: data = 12'h000;
        3'd1: data = 12'hFFF; 
        3'd2: data = 12'hACD; 
        3'd3: data = 12'h122; 
        3'd4: data = 12'h347; 
        3'd5: data = 12'h93A; 
        3'd6: data = 12'h0AF; 
        3'd7: data = 12'hC2B;
   endcase 
  end
endmodule
```
## An asynchronous ROM with two programs

As described above, this assignment is to create an asynchronous ROM containing the machine code of two
different RISC-V programs. Below are two RISC-V programs. Each one should be converted to machine code and 
then hard-coded into your asynchronous ROM. Then create a testbench to make sure that the ROM is working 
as expected.

Your asynchronous ROM should be deep enough to hold both programs so that they start on an even address and
wide enough to hold an instruction. To be specific lets make a 0x70 byte deep (or larger) and 4 byte (32-bit) wide ROM.
Addresses without an instruction should return 0.

### Program 1: Located at 0x0.
``` verilog
    addi t0, zero, 42
    addi t1, zero, 33
    add t2, t0, t1
```
### Program 2: Located at 0x40
```verilog
    add a0, zero, zero
    addi a1, a0, 4
    lw t0, 0(a0)
    lw t1, 0(a1)
    add t2, t1, t0
    andi t3, t2, 0xF0
    ori t4, t1, 0x0F0
    beq t2, t3, end
    add t4, t4, t0
end:sw t4, 4(a1)
```

## Hints on generating the machine code
Note that the beq instruction in program 2 will be dependent on the address of the instruction and
the address that is the destination of the branch. Refer back to the class activities where we did
these translations. This will be good practice for the exam where you will have to translate RISC V
instructions into and out of machine code.

Use similarities in the instructions to speed up your translations. Check with neighbors and the internet
to make sure these translations are correct.
