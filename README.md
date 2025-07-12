# ISAP-1 Computer Control Unit
The ISAP-1 computer is the improved version of the SAP-1 computer made by me.

ISAP Computer stands for Improved Simple as Possible Computer.

This is the stage where I designed the Control Unit block of the ISAP-1 computer.

By: Lincă Marius Gheorghe

https://github.com/LincaMarius

## About the project, brief description
The goal of this project is to create a more efficient version of the SAP (Simple As Possible) computer, but with minimal changes so that the instruction format remains the same.

This project helps build another project I made:
https://github.com/LincaMarius/ISAP-1_Computer_Project

where I build and test the functionality of the ISAP-1 computer.

## ISAP-1 computer version 0.1
The original format of the SAP-1 computer instructions is:

| 4 bits instruction code   | 4 bits operand (memory address)          |
|---------------------------|------------------------------------------|

We notice that the upper nibble is used to encode an instruction. 
So, any instruction is encoded on 4 bits. 
Thus, we can have a maximum of 2 ^ 4 = 16 instructions.

The codes for unimplemented instructions are treated by the SAP-1 computer as NOP instructions.

### Control Unit Structure
The Control Unit is built from three blocks:
- Step Counter,
- Control Matrix.

![ Figure 23 ](/Pictures/Figure23.png)

### Decoding the instruction
The format of the SAP-1 Computer instructions is:

| 4 bits instruction code   | 4 bits operand (memory address)          |
|---------------------------|------------------------------------------|

The Control Block receives the upper nibble of the instruction from the Instruction Register in the form of a group of 4 bits called INSTR.

These 4 bits represent the instruction encoding. In the original design of the SAP-1 computer, the authors used an instruction decoder.

Version 0.1 of the ISAP-1 Computer does not require an instruction decoder to function.

### Step Counter
For a computer to execute an instruction, it needs several steps. This is also the case for the SAP-1 computer, which needs 6 steps to execute the longest instructions.

For this purpose, a Step Counter is needed. The authors of the SAP-1 Computer used a Ring Counter to count the steps taken in executing an instruction.

The diagram of the described circuit made in the Logisim program is presented in figure 24.

![ Figure 24 ](/Pictures/Figure24.png)

### Control Matrix
The implementation of the Control Unit in the original schematic is done using Combinational Logic Circuits.

By making a circuit using Combinational Logic we mean using:
- Standard logic gates
- Decoders
- Demultiplexers
- Multiplexers

### Logical equations
The first step consists in determining the logic equations for the implemented circuit.

For this purpose we use the equations determined when we described the Instruction Set of the SAP-1 computer: \
https://github.com/LincaMarius/ISAP-1_Computer_Instruction_Set

from where we will combine all the equations for the instructions we want to implement.

### Adding the NOP instruction
We start from the NOP instruction equations:
-	EP = T1
-	LAR = T1
-	CP = T2
-	PM = T3
-	LI = T3

The “ + ” sign signifies the logical OR operation

The sign “ * ” stands for the logical AND operation

The following Control Matrix results, which uses 4 drivers because without them the Logisim program will show an error. The practical implementation does not require these drivers.

![ Figure 25 ](/Pictures/Figure25.png)












The equation in the left term represents the double negation which leads to the cancellation of the negation.

So, the term on the right that is present in each parenthesis in our equations can be replaced with the term on the left side in the figure above.

![ Figure 8 ](/Pictures/Figure8.png)

If we look at De Morgan's first Theorem we notice that the term on the right is a sum of negated elements.

Our equations now have negative terms added together. De Morgan's first theorem tells us that we can replace the equations with others where terms are multiplied and everything is negated.

![ Figure 9 ](/Pictures/Figure9.png)

Now all the equations have been modified to use only NAND gates, as you can see there are no more (+) symbols in our equations, so we no longer use OR gates.

The final schematic obtained is identical to the original schematic of the SAP-1 computer, except for the inversion of the control signals, because the blocks implemented in the Logisim program have positive control logic.

![ Figure 10 ](/Pictures/Figure10.png)

25 logic gates and 5 inverters are required to implement the original Control Matrix schematic for the SAP-1 computer.
