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

The diagram of the described circuit made in the Logisim program is presented in figure 4.

![ Figure 4 ](/Pictures/Figure4.png)

### Control Matrix
Implementation of the Control Unit using Combinational Logic Circuits

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

### Adding the LDA instruction
We add the LDA instruction that has the following Boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + LDA * T4
-	CP = T2
-	PM = T3 + LDA * T5
-	LI = T3
-	EI = LDA * T4
-	LA = LDA * T5

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T4
-	CP = T2
-	PM = T3 + LDA * T5
-	LI = T3
-	EI = LDA * T4
-	LA = LDA * T5

### Adding the ADD instruction
We add the ADD instruction that has the following Boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + ADD * T4
-	CP = T2
-	PM = T3 + ADD * T5
-	LI = T3
-	EI = ADD * T4
-	LB = ADD * T5
-	EU = ADD * T6
-	LA = ADD * T6

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T4 + ADD * T4
-	CP = T2
-	PM = T3 + LDA * T5 + ADD * T5
-	LI = T3
-	EI = LDA * T4 + ADD * T4
-	LB = ADD * T5
-	EU = ADD * T6
-	LA = LDA * T5 + ADD * T6

### Adding the SUB instruction
We add the SUB instruction that has the following Boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + SUB * T4
-	CP = T2
-	PM = T3 + SUB * T5
-	LI = T3
-	EI = SUB * T4
-	LB = SUB * T5
-	EU = SUB * T6
-	LA = SUB * T6
-	SU = SUB * T6

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T4 + ADD * T4 + SUB * T4
-	CP = T2
-	PM = T3 + LDA * T5 + ADD * T5 + SUB * T5
-	LI = T3
-	EI = LDA * T4 + ADD * T4 + SUB * T4
-	LB = ADD * T5 + SUB * T5
-	EU = ADD * T6 + SUB * T6
-	LA = LDA * T5 + ADD * T6 + SUB * T6
-	SU = SUB * T6

### Adding the OUT instruction
We add the OUT instruction that has the following Boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	CP = T2
-	PM = T3
-	LI = T3
-	EA = OUT * T4
-	I/O = OUT * T4

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T4 + ADD * T4 + SUB * T4
-	CP = T2
-	PM = T3 + LDA * T5 + ADD * T5 + SUB * T5
-	LI = T3
-	EI = LDA * T4 + ADD * T4 + SUB * T4
-	LB = ADD * T5 + SUB * T5
-	EU = ADD * T6 + SUB * T6
-	LA = LDA * T5 + ADD * T6 + SUB * T6
-	SU = SUB * T6
-	EA = OUT * T4
-	I/O = OUT * T4

### Adding the HLT instruction
We add the HLT instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T4 + ADD * T4 + SUB * T4
-	CP = T2
-	PM = T3 + LDA * T5 + ADD * T5 + SUB * T5
-	LI = T3
-	EI = LDA * T4 + ADD * T4 + SUB * T4
-	LB = ADD * T5 + SUB * T5
-	EU = ADD * T6 + SUB * T6
-	LA = LDA * T5 + ADD * T6 + SUB * T6
-	SU = SUB * T6
-	EA = OUT * T4
-	I/O = OUT * T4
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6

The " + " sign signifies the logical OR operation.

The sign " * " stands for the logical AND operation.

### Optimizing equations
If we look at the final equations we can easily conclude that we will need many logic gates to implement this circuit.

To obtain the final implementation schematic of the Control Matrix, several steps must be completed.

### Ordering the terms of the equations
Before doing anything else it is necessary to order the terms that make up the above equations.

I ordered the equations in ascending order of T elements:
-	EP = T1
-	LAR = T1 + LDA * T4 + ADD * T4 + SUB * T4
-	CP = T2
-	PM = T3 + LDA * T5 + ADD * T5 + SUB * T5
-	LI = T3
-	EI = LDA * T4 + ADD * T4 + SUB * T4
-	LB = ADD * T5 + SUB * T5
-	EU = ADD * T6 + SUB * T6
-	LA = LDA * T5 + ADD * T6 + SUB * T6
-	SU = SUB * T6
-	EA = OUT * T4
-	I/O = OUT * T4
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6

The authors of the SAP-1 computer used the HLT signal to inhibit the clock signal, so it does not need to be processed further because it is not an input to the Control Matrix.

From these equations, the Control Matrix can be implemented.

I will separate all terms containing the (*) sign which represents a two-input AND gate.

-	EP = T1
-	LAR = T1 + (LDA * T4) + (ADD * T4) + (SUB * T4)
-	CP = T2
-	PM = T3 + (LDA * T5) + (ADD * T5) + (SUB * T5)
-	LI = T3
-	EI = (LDA * T4) + (ADD * T4) + (SUB * T4)
-	LB = (ADD * T5) + (SUB * T5)
-	EU = (ADD * T6) + (SUB * T6)
-	LA = (LDA * T5) + (ADD * T6) + (SUB * T6)
-	SU = (SUB * T6)
-	EA = (OUT * T4)
-	I/O = (OUT * T4)

Each pair of parentheses represents an AND gate. Now each signal represents the output of an OR gate with two or more inputs. If we have a single (+) sign we have a 2-input OR gate. For each (+) sign, one more input is added.

The result is the following Control Matrix that uses 19 AND gates with 2 inputs, 2 OR gates with 4 inputs, 2 OR gates with 3 inputs and 2 OR gates with 2 inputs, a total of 25 logic gates.

![ Figure 5 ](/Pictures/Figure5.png)

But this is not identical to that shown in the original schematic of the SAP-1 computer.

The authors used NAND gates instead of AND gates in their implementation. How can we modify the schematic to use NAND gates?

The solution is to use De Morgan's theorems:

![ Figure 6 ](/Pictures/Figure6.png)

From our equations we have obtained terms of the form (LDA * T4) which represents a two-input AND gate. But we want to use NAND gates instead of AND gates. The solution is to use a NAND gate followed by an inverter, thus preserving the previous function.

![ Figure 7 ](/Pictures/Figure7.png)

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
