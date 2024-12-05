# ISAP-1 Computer Control Unit
The ISAP-1 computer is the improved version of the SAP-1 computer made by me.

ISAP Computer stands for Improved Simple as Possible Computer.

This is the stage where we designed the Control Unit block of the ISAP-1 computer.

By: Lincă Marius Gheorghe.

Pitești, Argeș, România, Europe.

https://github.com/LincaMarius

## About the project, brief description
The goal of this project is to create a more efficient version of the SAP (Simple As Possible) computer, but with minimal changes so that the instruction format remains the same.

This project helps build another project I made:
https://github.com/LincaMarius/ISAP-1_Logisim

where I build and test the functionality of the ISAP-1 computer using the Logisim program.

## Revision A Version 1

### The original format of the SAP-1 computer instructions is:

| 4 bits instruction code   | 4 bits operand (memory address)          |
|---------------------------|------------------------------------------|

We notice that the upper nibble is used to encode an instruction. 
So, any instruction is encoded on 4 bits. 
Thus, we can have a maximum of 2 ^ 4 = 16 instructions.

### The original instruction set of the SAP-1 computer is:

| Mnemonic | Opcode | Operation                                  |
|----------|--------|--------------------------------------------|
| LDA      | 0000   | Load RAM data into Accumulator             |
| ADD      | 0001   | Add RAM data to Accumulator                |
| SUB      | 0010   | Substract RAM data from accumulator        |
| OUT      | 1110   | Load Accumulator data into Output Register |
| HLT      | 1111   | Stop processing                            |

We can notice that the instructions 0011, 0100, 0101, 0110, 0111, 1000, 1001, 1010, 1011, 1100, 1101 are not used. So we can add 11 more new instructions.

These codes are treated by the SAP-1 computer as NOP instructions.

### Decoding the instruction
The format of the SAP-1 Computer instructions is:

| 4 bits instruction code   | 4 bits operand (memory address)          |
|---------------------------|------------------------------------------|

The Control Block receives the upper nibble of the instruction from the Instruction Register in the form of a group of 4 bits called INSTR.

These 4 bits represent the instruction encoding. In the original design of the SAP-1 computer, the authors used an instruction decoder.

To design this Instruction Decoder we need to create a table in which we have all the outputs depending on the inputs. This is the Truth Table for the designed decoder.

![ Table 1 ](/Tables/Table1.png)

In this table I put all the implemented instructions on the first column. On the second column I put all the possible 4-bit codes. It represents the input to the designed Decoder.

In the next four columns, the values ​​in the binary code of the input instruction are associated with the bits received at the Decoder input.

The last five columns represent the binary state at the output of the Decoder.

When the input presents a code of an implemented and valid instruction, we want to get one at the output, so in the last columns we put one only on the table lines where we have an implemented instruction.

We count in the last columns of the table how many ones and how many zeros we have. We have one for each output and 15 zero values ​​in each column corresponding to an output.

To reduce the number of logic gates used in the implementation, we choose the smaller number, i.e. 1. So the implemented decoder will output a logical one only for the 5 input codes that correspond in the table with the value 1 for each output.

Since we are not interested in zero-valued outputs, we will remove from the table all lines that have only zeros in the positions in the last columns of the table.

The work table now looks like this:

![ Table 2 ](/Tables/Table2.png)

Now each line in the table is processed separately.

For example, the first line for the LDA instruction we observe that if at the input we have the binary code 0000 corresponding to the LDA instruction at the output of the Decoder only the LDA output is active.

Each line in the table from an implementation point of view can be associated with an AND gate if we want an active output up or with an OR gate if we want an active output down.

In this case, we want the output to be active high so we need to use an AND gate.

We need a four-input AND gate, and we will use the Truth Table for a four-input AND gate:

![ Table 3 ](/Tables/Table3.png)

From Table 3 it can be seen that to obtain a logical one at the output, all inputs must be logical ones.

But for the LDA instruction all the inputs will be logical zero. The solution is to invert all those inputs that are zero, and the inputs that are ones are not inverted.

Now we will extract from the table the Functions that control the outputs of the Decoder according to the inputs to the Decoder. We apply the rule that for each input that has a zero, that input is inverted, and inputs that have a one are not inverted.

LDA = /I7 * /I6 * /I5 * /I4 \
ADD = /I7 * /I6 * /I5 * I4 \
SUB = /I7 * /I6 * I5 * /I4 \
OUT = I7 * I6 * I5 * /I4 \
HLT = I7 * I6 * I5 * I4

The diagram of the described circuit made in the Logisim program is presented in figure 1.

![ Figure 1 ](/Pictures/Figure1.png)

In the original scheme, the authors used 5 NAND gates with 4 inputs marked C32, C33 C34 and used the 74LS20 integrated circuit according to the Parts List which is presented in the book in Appendix 5 on page 501.

As a result, it is necessary to invert the outputs of the NAND gates to obtain the correct output signals. For this purpose, the authors used 4 inverters denoted in the schematic C35 using the 74LS04 integrated circuit.

A better option is to use 4-input AND gates like the 74LS21. This way you don't need the C35 chip.

I made the original version of the Instruction Decoder which is shown in the following figure

![ Figure 2 ](/Pictures/Figure2.png)

### Step Counter
For a computer to execute an instruction, it needs several steps. This is also the case for the SAP-1 computer, which needs 6 steps to execute the longest instructions.

For this purpose, a Step Counter is needed. The authors of the SAP-1 Computer used a Ring Counter to count the steps taken in executing an instruction.

The diagram of the described circuit made in the Logisim program is presented in figure 2.

![ Figure 3 ](/Pictures/Figure3.png)

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

The + sign signifies the logical OR operation.

The sign * stands for the logical AND operation.

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
We add the SUB instruction that has the following boolean equations for the signals that are active when this instruction is executed:
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
We add the OUT instruction that has the following boolean equations for the signals that are active when this instruction is executed:
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
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8

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
-	HLT = HLT * T4 + HLT * T5 + HLT * T6

### Optimizing equations
If we look at the final equations we can easily conclude that we will need many logic gates to implement this circuit.

To obtain the final implementation scheme of the Control Matrix, several steps must be completed.

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
-	HLT = HLT * T4 + HLT * T5 + HLT * T6

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

Each parenthesis represents an AND gate. Now each signal represents the output of an OR gate with two or more inputs. If we have a single (+) sign we have a 2-input OR gate. For each (+) sign, one more input is added.

The result is the following Control Matrix that uses 19 AND gates with 2 inputs, 2 OR gates with 4 inputs, 2 OR gates with 3 inputs and 2 OR gates with 2 inputs, a total of 25 logic gates.

![ Figure 4 ](/Pictures/Figure4.png)

But this is not identical to that shown in the original schematic of the SAP-1 computer.

The authors used NAND gates instead of AND gates in their implementation. How can we modify the scheme to use NAND gates?

The solution is to use De Morgan's theorems:

![ Figure 5 ](/Pictures/Figure5.png)

From our equations we have obtained terms of the form (LDA * T4) which represents a two-input AND gate. But we want to use NAND gates instead of AND gates. The solution is to use a NAND gate followed by an inverter, thus preserving the previous function.

![ Figure 6 ](/Pictures/Figure6.png)

The equation in the left term represents the double negation which leads to the cancellation of the negation.

So, the term on the right that is present in each parenthesis in our equations can be replaced with the term on the left side in the figure above.

![ Figure 7 ](/Pictures/Figure7.png)

If we look at De Morgan's first Theorem we notice that the term on the right is a sum of negated elements.

Our equations now have negative terms added together. De Morgan's first theorem tells us that we can replace the equations with others where terms are multiplied and everything is negated.

![ Figure 8 ](/Pictures/Figure8.png)

Now all the equations have been modified to use only NAND gates, as you can see there are no more (+) symbols in our equations, so we no longer use AND gates.

The final schematic is identical to the original SAP-1 computer schematic, except for the inversion of the control signals, because the blocks implemented in the Logisim program have positive control logic.

![ Figure 9 ](/Pictures/Figure9.png)

25 logic gates and 5 inverters are required to implement the original Control Block diagram for the SAP-1 computer.

## Revision B Version 1
Revision B is an implementation of the Control Unit using a ROM memory instead of logic gates as we did in the implementation of revision A.

We group all control signals into a control word that the authors of the SAP-1 computer called “CON”

The structure of the CON control word is: CpEp#Lm#CE #Li#Ei#LaEa SuEu#Lb#Lo, where # signals an active low signal.

The idle state in which no output is active is as follows: 0011 1110 0011

In hexadecimal this represents: 0x3E3

Activating a control signal will cause the bit associated with it in the CON control word to be inverted.

The Fetch sequence has three steps:
- Ep, #Lm
- Cp
- #CE, #Li

If we invert the corresponding bits in CON we get:
- 0x5E3
- 0xBE3
- 0x263

To make things easier, we have created the following helpful table:

| Address | Routine | Active signals | Cp | Ep | #Lm | #CE | #Li | #Ei | #La | Ea | Su | Eu | #Lb | #Lo | CON controls |
|---------|---------|----------------|----|----|-----|-----|-----|-----|-----|----|----|----|-----|-----|--------------|
|   -     |     -   |       -        |  0 | 0  |  1  |  1  |  1  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 3E3   |
|   0x0   |  Fetch  |  Ep, #Lm       |  0 | 1  |  0  |  1  |  1  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 5E3   |
|   0x1   |         |  Cp            |  1 | 0  |  1  |  1  |  1  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x BE3   |
|   0x2   |         |  #CE, #Li      |  0 | 0  |  1  |  0  |  0  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 263   |
|   0x3   |   LDA   |  #Lm, #Ei      |  0 | 0  |  0  |  1  |  1  |  0  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 1A3   |
|   0x4   |         |  #CE, #La      |  0 | 0  |  1  |  0  |  1  |  1  |  0  | 0  | 0  | 0  |  1  |  1  |    0 x 2C3   |     
|   0x5   |         |       -        |  0 | 0  |  1  |  1  |  1  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 3E3   |
|   0x6   |   ADD   |  #Lm, #Ei      |  0 | 0  |  0  |  1  |  1  |  0  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 1A3   |
|   0x7   |         |  #CE, #Lb      |  0 | 0  |  1  |  0  |  1  |  1  |  1  | 0  | 0  | 0  |  0  |  1  |    0 x 2E1   |
|   0x8   |         |  #La, Eu       |  0 | 0  |  1  |  1  |  1  |  1  |  0  | 0  | 0  | 1  |  1  |  1  |    0 x 3C7   |
|   0x9   |   SUB   |  #Lm, #Ei      |  0 | 0  |  0  |  1  |  1  |  0  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 1A3   |
|   0xA   |         |  #CE, #Lb      |  0 | 0  |  1  |  0  |  1  |  1  |  1  | 0  | 0  | 0  |  0  |  1  |    0 x 2E1   |
|   0xB   |         |  #La, Su, Eu   |  0 | 0  |  1  |  1  |  1  |  1  |  0  | 0  | 1  | 1  |  1  |  1  |    0 x 3CF   |
|   0xC   |   OUT   |  Ea, #Lo       |  0 | 0  |  1  |  1  |  1  |  1  |  1  | 1  | 0  | 0  |  1  |  0  |    0 x 3F2   |
|   0xD   |         |       -        |  0 | 0  |  1  |  1  |  1  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 3E3   |
|   0xE   |         |       -        |  0 | 0  |  1  |  1  |  1  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 3E3   |
|   0xF   |    -    |       -        |  0 | 0  |  1  |  1  |  1  |  1  |  1  | 0  | 0  | 0  |  1  |  1  |    0 x 3E3   |

We have 16 addresses and a 12-bit word must be stored at each address

So to address 16 memory locations we need 4 address pins

In this table we have microinstructions for the Fetch routine between address 0h and 2h, for the LDA instruction at address 3h to 5h, for the ADD instruction at address 6h to 8h, for the SUB instruction at address 9h to Bh, for the OUT instruction at address Ch to Eh.

For implementation in the book, the authors present us with a diagram marked Figure 10-16 on page 162.

We also need a 4-bit presettable counter and a 16x4 ROM memory.

The address ROM stores the starting address for each routine shown in the previous table.

The contents of the address ROM are as follows:

| Address | hexa | Routine | Contents | hexa |
|---------|------|---------|----------|------|
|  0000   |  0h  |   LDA   |   0011   |  3h  |
|  0001   |  1h  |   ADD   |   0110   |  6h  |
|  0010   |  2h  |   SUB   |   1001   |  9h  |
|  0011   |  3h  |    -    |   1111   |  Fh  |
|  0100   |  4h  |    -    |   1111   |  Fh  |
|  0101   |  5h  |    -    |   1111   |  Fh  |
|  0110   |  6h  |    -    |   1111   |  Fh  |
|  0111   |  7h  |    -    |   1111   |  Fh  |
|  1000   |  8h  |    -    |   1111   |  Fh  |
|  1001   |  9h  |    -    |   1111   |  Fh  |
|  1010   |  Ah  |    -    |   1111   |  Fh  |
|  1011   |  Bh  |    -    |   1111   |  Fh  |
|  1100   |  Ch  |    -    |   1111   |  Fh  |
|  1101   |  Dh  |    -    |   1111   |  Fh  |
|  1110   |  Eh  |   OUT   |   1100   |  Ch  |
|  1111   |  Fh  |    -    |   1111   |  Fh  |

The address of the ROM that generates the addresses is connected to the output of the Instruction Register.

The Control Block implementation in the Logisim program is as follows:

![ Figure 10 ](/Pictures/Figure10.png)

The operation of the ISAP-1 revision B version 1 computer was verified with the new Control Block.

The contents of the ROM for Address generation are: \
[ AdrROM1 ](/ROMS/AdrROM1)

The contents of the Control ROM are: \
[ CtrlROM1 ](/ROMS/CtrlROM1)
