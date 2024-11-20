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

## Version 1

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




### Finding duplicate AND gates
A first optimization is to find repeating terms. These terms are of the form “LDA * T4” and represent a two-input AND gate.

Once a term has been calculated we can use it in all equations where it appears.

For this purpose we replace the repeating term with a letter of the alphabet wherever this term appears. We repeat this process until we find no more repeating terms.

L3 = LDA * T3 \
A3 = ADD * T3 \
S3 = SUB * T3 \
O3 = OUT * T3 \
B3 = IN * T3 \
K3 = STA * T3 \
F3 = CMP * T3 \
D3 = LIL * T3 \
H3 = LIH * T3 \
J3 = JMP * T3 \
Z3 = JZ * T3 \
M3 = JC * T3 \
Q3 = JS * T3 \
E3 = DEC * T3 \
N3 = NEG * T3 \
G3 = INC * T3 \
C3 = CPY * T3 \

L4 = LDA * T4 \
A4 = ADD * T4\
A5 = ADD * T5\
S4 = SUB * T4\
S5 = SUB * T5\
O4 = OUT * T4\
B4 = IN * T4\
K4 = STA * T4\
F4 = CMP * T4\
F5 = CMP * T5\
E4 = DEC * T4\
N4 = NEG * T4\
N5 = NEG * T5\
G4 = INC * T4\


This represents 31 2-input AND gates.

If we replace new created terms in the above equations we get:
-	EP = T1
-	LAR = T1 + L3 + A3 + S3 + O3 + B3 + K3 + F3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = L3 + A3 + S3 + O3 + D3 + H3 + B3 + J3 + K3 + F3 + Z3 + M3 + Q3
-	DM = L4 + A4 + S4 + K4 + F4
-	LAH = H3 + L4 + B4 + E4 + N4 + A5 + S5 + N5
-	LAL = D3 + L4 + B4 + G4 + E4 + N4 + A5 + S5 + N5
-	LB = C3 + G3 + E3 + N3 + A4 + S4 + F4
-	EU = G4 + E4 + A5 + S5 + N5
-	SU = E4 + S5 + F5 + N5
-	EA = C3 + N3 + O4 + K4
-	I/O = O4 + B4
-	R/W = O4 + K4
-	LP = J3 + Z * Z3 + C * M3 + S * Q3
-	EC = G3 + E3 + N4
-	SC1 = G3 + E3

You will need:
- 3 AND gates with 2 inputs
- 3 OR gates with 2 inputs
- 1 OR gate with 3 inputs
- 3 OR gates with 4 inputs
- 2 OR gates with 5 inputs
- 1 OR gate with 7 inputs
- 2 OR gates with 8 inputs
- 1 OR gate with 9 inputs
- 1 OR gate with 13 inputs

A total of 17 + 31 = 48 logic gates are required. 

This is version 1 of the implementation.

implementation of HLT and NEXT signals will be treated separately

### Minimizing the number of OR gates
As we did previously, we will look for groups of terms that appear in multiple equations and assign them a name.

W0 = G3 + E3\
K0 = O4 + K4\
Y0 = E4 + S5 + N5\
P0 = G4 + A5\
W1 = A4 + S4 + F4\
K1 = L4 + B4 + N4\
Y1 = L3 + A3 + S3 + O3 + B3 + K3 + F3\
Y2 = Y0 + K1
P1 = C3 + N3

You will need:
- 5 OR gates with 2 inputs
- 3 OR gate with 3 inputs
- 1 OR gate with 7 inputs

A total of 9 logic gates are required.

If we replace new terms created in the above equations we get:
-	EP = T1
-	LAR = T1 + Y1
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = Y1 + D3 + H3 + J3 + Z3 + M3 + Q3
-	DM = L4 + W1 + K4
-	LAH = Y2 + H3 + A5
-	LAL = Y2 + D3 + P0
-	LB = P1 + W0 + W1
-	EU = Y0 + P0
-	SU = Y0 + F5
-	EA = P1 + K0
-	I/O = O4 + B4
-	R/W = K0
-	LP = J3 + Z * Z3 + C * M3 + S * Q3
-	EC = W0 + N4
-	SC1 = W0

You will need:
- 3 AND gates with 2 inputs
- 6 OR gates with 2 inputs
- 4 OR gate with 3 inputS
- 1 OR gates with 4 inputs
- 1 OR gates with 7 inputs

A total of 15 + 9 + 31 = 55 logic gates are required.

This is version 2 of the implementation.

### Implementation optimization
We will start from the previous equations where we will give common factor steps T.
-	EP = T1
-	LAR = T1 + T3 * (LDA + ADD + SUB + OUT + IN + STA + CMP)
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = T3 * (LDA + ADD + SUB + OUT + LIL + LIH + IN + JMP + STA + CMP + JZ + JC + JS)
-	DM = T4 * (LDA + ADD + SUB + STA + CMP)
-	LAH = LIH * T3 + T4 * (LDA + IN + DEC + NEG) + T5 * (ADD + SUB + NEG)
-	LAL = LIL * T3 + T4 * (LDA + IN + INC + DEC + NEG) + T5 * (ADD + SUB + NEG)
-	LB = T3 * (CPY + INC + DEC + NEG) + T4 * (ADD + SUB + CMP)
-	EU = T4 * (INC + DEC) + T5 * (ADD + SUB + NEG)
-	SU = DEC * T4 + T5 * (SUB + CMP + NEG)
-	EA = T3 * (CPY + NEG) + T4 * (OUT + STA)
-	I/O = T4 * (OUT + IN)
-	R/W = T4 * (OUT + STA)
-	LP = T3 * (JMP + Z * JZ + C * JC + S * JS)
-	EC = T3 * (INC + DEC) + NEG * T4
-	SC1 = T3 * (INC + DEC)

You will need:
- 22 AND gates with 2 inputs
- 12 OR gates with 2 inputs
- 4 OR gate with 3 inputs
- 2 OR gates with 4 inputs
- 1 OR gates with 5 inputs
- 1 OR gate with 7 inputs
- 1 OR gate with 13 inputs

A total of 43 logic gates are required.

This is version 3 of the implementation.

### Minimizing the number of OR gates
As we did previously, we will look for groups of terms that appear in multiple equations and assign them a name.

S0 = ADD + SUB \
W0 = LDA + S0 + STA + CMP \
K0 = OUT + IN \
L0 = INC + DEC \
M0 = OUT + STA \
R0 = S0 + NEG \
A0 = LDA + NEG \
B0 = CPY + NEG \
C0 = W0 + K0 \
D0 = A0 + IN

You will need:
- 9 OR gates with 2 inputs
- 1 OR gates with 4 inputs

A total of 10 logic gates are required.

-	EP = T1
-	LAR = T1 + T3 * C0
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = T3 * (C0 + LIL + LIH + JMP + JZ + JC + JS)
-	DM = T4 * W0
-	LAH = LIH * T3 + T4 * (D0 + DEC) + T5 * R0
-	LAL = LIL * T3 + T4 * (D0 + L0) + T5 * R0
-	LB = T3 * (B0 + L0) + T4 * (S0 + CMP)
-	EU = T4 * L0 + T5 * R0
-	SU = DEC * T4 + T5 * (SUB + CMP + NEG)
-	EA = T3 * B0 + T4 * M0
-	I/O = T4 * K0
-	R/W = T4 * M0
-	LP = T3 * (JMP + Z * JZ + C * JC + S * JS)
-	EC = T3 * L0 + NEG * T4
-	SC1 = T3 * L0

You will need:
- 26 AND gates with 2 inputs
- 10 OR gates with 2 inputs
- 3 OR gate with 3 inputs
- 1 OR gates with 4 inputs
- 1 OR gate with 7 inputs

A total of 41 + 10 = 51 logic gates are required.

This is version 4 of the implementation.

So the previous version is superior.

### The implementation of the HLT signal
The implementation of the HLT signal: 
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8

requires:
- 6 AND gates with 2 inputs
- 1 OR gate with 6 inputs

This is version 1 of the implementation.

This equation can also be written as:
-	HLT = HLT * (T3 + T4 + T5 + T6 + T7 + T8)

requires:
- 1 AND gates with 2 inputs
- 1 OR gate with 6 inputs

This is version 2 of the implementation.

The simplest variant is:
-	HLT = HLT * T3

requires:
- 1 AND gates with 2 inputs

This is version 3 of the implementation.

### The implementation of the NEXT signal
To implement the NEXT control signal according to the resulting equation:
-	NEXT = (NOP * T3) + (NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + JS * T4) + (NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + JC * T5 + JS * T5 + INC * T5 + DEC * T5) + (NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + JC * T6 + JS * T6 + INC * T6 + DEC * T6 + NEG * T6) + (NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + JC * T7 + JS * T7 + INC * T7 + DEC * T7 + NEG * T7) + (NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8 + JC * T8 + JS * T8 + INC * T8 + DEC * T8 + NEG * T8)

We will need:
- 77 AND gateswith 2 inputs,
- 1 OR gates with 14 inputs,
- 1 OR gates with 8 inputs,
- 3 OR gates with 18 inputs
- 1 OR gates with 6 inputs

A total of 83 logic gates are required.

This is version 1 of the implementation.

If we optimize the equation, the new equation results:
-	NEXT = (NOP * T3) + (T4 * (NOP + LIL + LIH + JMP + CPY + JZ + JC + JS)) + (T5* (NOP + LDA + OUT + LIL + LIH + IN + JMP + STA + CPY + JZ + JC + JS + INC + DEC)) + (T6* (NOP + LDA + ADD + SUB + OUT + LIL + LIH + IN + JMP + STA + CMP + CPY + JZ + JC + JS + INC + DEC + NEG)) + (T7 * (NOP + LDA + ADD + SUB + OUT + LIL + LIH + IN + JMP + STA + CMP + CPY + JZ + JC + JS + INC + DEC + NEG)) + ( T8 * (NOP + LDA + ADD + SUB + OUT + LIL + LIH + IN + JMP + STA + CMP + CPY + JZ + JC + JS + INC + DEC + NEG))

We will need:
- 6 AND gateswith 2 inputs,
- 1 OR gates with 14 inputs,
- 1 OR gates with 8 inputs,
- 3 OR gates with 18 inputs
- 1 OR gates with 6 inputs

A total of 12 logic gates are required.

This is version 2 of the implementation.

### The SIMPLIFIED implementation of the NEXT signal
One can reduce the number of logic gates required by enabling the NEXT control signal only one step.
We will have the following equation:
-	NEXT = (NOP * T3) + (LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + JS * T4) + (LDA * T5 + OUT * T5 + IN * T5 + STA * T5 + INC * T5 + DEC * T5) + (ADD * T6 + SUB * T6 + CMP * T6 + NEG * T6)

in this form we will need:
- 18 AND gateswith 2 inputs
- 1 OR gates with 6 inputs
- 1 OR gates with 7 inputs
- 2 OR gates with 4 inputs

A total of 22 logic gates are required.

This is version 3 of the implementation.

This equation can be written as:
-	NEXT = (NOP * T3) + T4 * (LIL + LIH + JMP + CPY + JZ + JC + JS) + T5 * (LDA + OUT + IN + STA + INC + DEC) + T6 * (ADD + SUB + CMP + NEG)

in this form we will need:
- 4 AND gateswith 2 inputs
- 1 OR gates with 6 inputs
- 1 OR gates with 7 inputs
- 2 OR gates with 4 inputs

A total of 8 logic gates are required.

This is version 4 of the implementation.

## Simulation in Logisim
For the implementation and simulation in the Logisim program I will use:
- Version 3 for the control matrix
- Version 3 for HLT signal generation
- Version 4 for NEXT signal generation


