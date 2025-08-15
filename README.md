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

I treated the Control Block design separately because it is more complicated and involves several distinct stages.

Here I designed the Control Block from scratch starting from the Boolean equations determined in step 1 when I studied the instruction set of the SAP-1 computer.

## ISAP-1 computer version 0.1
The goal is to design the Control Block that uses only logic gates (Combinational Logic) exactly as the authors of the SAP-1 computer did.

The original format of the SAP-1 computer instructions is:

| 4 bits instruction code   | 4 bits operand (memory address)          |
|---------------------------|------------------------------------------|

We notice that the upper nibble is used to encode an instruction. 
So, any instruction is encoded on 4 bits. 
Thus, we can have a maximum of 2 ^ 4 = 16 instructions.

The codes for unimplemented instructions are treated by the SAP-1 computer as NOP instructions.

### The SAP-1 Computer Instruction Set
The original instruction set of the SAP-1 computer is:
| Mnemonic | Opcode | Operation                                  |
|----------|--------|--------------------------------------------|
| LDA      | 0000   | Load RAM data into Accumulator             |
| ADD      | 0001   | Add RAM data to Accumulator                |
| SUB      | 0010   | Substract RAM data from accumulator        |
| OUT      | 1110   | Load Accumulator data into Output Register |
| HLT      | 1111   | Stop processing                            |

We can see that out of the 16 possible instructions, only 5 are implemented. So, we can add 11 more new instructions.

Codes for unimplemented instructions are treated by the SAP-1 computer as NOP instructions.

### Control Unit Structure
The Control Unit is built from three blocks:
- Instruction Decoder,
- Step Counter,
- Control Matrix.

![ Figure 15 ](https://github.com/LincaMarius/ISAP-1_Block_Diagram/blob/main/Pictures/Figure15.png)

### Decoding the instruction
The format of the SAP-1 Computer instructions is:

| 4 bits instruction code   | 4 bits operand (memory address)          |
|---------------------------|------------------------------------------|

The Control Block receives the upper nibble of the instruction from the Instruction Register in the form of a group of 4 bits called "INSTR".

These 4 bits represent the instruction encoding. In the original design of the SAP-1 computer, the authors used an instruction decoder.

To design this Instruction Decoder we need to create a table in which we have all the outputs depending on the inputs. This is the Truth Table for the designed decoder.

![ Table 7 ](/Tables/Table7.png)

In this table I put all the implemented instructions on the first column. On the second column I put all the possible 4-bit codes. It represents the input to the designed Decoder.

In the next four columns, the values in the binary code of the input instruction are associated with the bits received at the Decoder input.

The last five columns represent the binary state at the output of the Decoder.

When the input presents a code of an implemented and valid instruction, we want to get one at the output, so in the last columns we put one only on the table lines where we have an implemented instruction.

Since we are not interested in zero-valued outputs, we will remove from the table all lines that have only zeros in the positions in the last columns of the table.

The work table now looks like this:

![ Table 8 ](/Tables/Table8.png)

Now the output from the table is processed separately.

For example, the first line for the LDA instruction we observe that if at the input we have the binary code 0000 corresponding to the LDA instruction at the output of the Decoder only the LDA output is active.

Each line in the table from an implementation point of view can be associated with an AND gate because we want an active output high.

We need a four-input AND gate, and we will use the Truth Table for a four-input AND gate:

![ Table 9 ](/Tables/Table9.png)

From Table 9 it can be seen that to obtain a logical one at the output, all inputs must be logical ones.

But for the LDA instruction all the inputs will be logical zero. The solution is to invert all those inputs that are zero, and the inputs that are ones are not inverted.

 Now we will extract from the table the Functions that control the outputs of the Decoder according to the inputs to the Decoder. We apply the rule that for each input that has a zero, that input is inverted, and inputs that have a one are not inverted.

![ Figure 27 ](/Pictures/Figure27.png)

The diagram of the described circuit made in the Logisim program is presented in figure 28.

![ Figure 28 ](/Pictures/Figure28.png)

It should be noted that in the original design of the SAP-1 computer, the authors used NAND gates. This is common in the electronics industry because it reduces the diversity of chips used by reducing the BOM (Bill of Materials).

In the original scheme, the authors used 5 NAND gates with 4 inputs marked C32, C33, C34 and used the 74LS20 integrated circuit according to the Parts List which is presented in the book in Appendix 5 on page 501.

As a result, it is necessary to invert the outputs of the NAND gates to obtain the correct output signals. For this purpose, the authors used 4 inverters denoted in the schematic C35 using the 74LS04 integrated circuit.

I made the original version of the Instruction Decoder which is shown in the following figure

![ Figure 29 ](/Pictures/Figure29.png)

### Step Counter
For a computer to execute an instruction, it needs several steps. This is also the case for the SAP-1 computer, which needs 6 steps to execute the longest instructions.

For this purpose, a Step Counter is needed. The authors of the SAP-1 Computer used a Ring Counter to count the steps taken in executing an instruction.

The Ring Counter is made by chaining 6 J-K type flip-flops.

The circuit diagram described is shown in the following figure.

![ Figure 30 ](/Pictures/Figure30.png)

### Control Matrix
Implementation of the Control Unit using Combinational Logic Circuits, meaning elementary logic gates were used.

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
Next I will create the equation of the Boolean function that defines the Control Matrix.

We start from the NOP instruction equations:
-	EP = T1
-	LAR = T1
-	CP = T2
-	PM = T3
-	LI = T3

The “ + ” sign signifies the logical OR operation

The sign “ * ” stands for the logical AND operation

### Adding the LDA instruction
We add the LDA instruction that has the following boolean equations for the signals that are active when this instruction is executed:
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
We add the ADD instruction that has the following boolean equations for the signals that are active when this instruction is executed:
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
From the SAP-1 computer diagram, we noted that the HLT signal given by the Instruction Decoder is used to lock the clock signal. So this instruction has no influence on the implementation of the Control Matrix.

## Optimizing equations
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

![ Figure 31 ](/Pictures/Figure31.png)

But this is not identical to that shown in the original schematic of the SAP-1 computer.

The authors used NAND gates instead of AND gates in their implementation. How can we modify the schematic to use NAND gates?

The solution is to use De Morgan's theorems

From our equations we have obtained terms of the form (LDA * T4) which represents a two-input AND gate. But we want to use NAND gates instead of AND gates. The solution is to use a NAND gate followed by an inverter, thus preserving the previous function.

![ Figure 32 ](/Pictures/Figure32.png)



The following Control Matrix results, which uses 4 drivers because without them the Logisim program will show an error. The practical implementation does not require these drivers.

![ Figure 25 ](/Pictures/Figure25.png)

Therefore, the final structure of the Control Unit used to implement the ISAP-1 Computer version 0.1 is:

![ Figure 26 ](/Pictures/Figure26.png)
