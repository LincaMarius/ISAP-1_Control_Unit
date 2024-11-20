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

The last column represents the binary state at the output of the Decoder.

When the input presents a code of an implemented instruction, we want to get one at the output, so in the last column we put one only on the table lines where we have an implemented instruction.

We count in the last column of the table how many ones and how many zeros we have. We have 5 values ​​of ones and 11 values ​​of zeros.

To reduce the number of logic gates used in the implementation, we choose the smaller number, i.e. 5. So, the implemented decoder will output a logical one only for the 5 input codes that correspond in the table with the value 1.

Since we are not interested in zero-valued outputs, we will remove from the table all lines that have zero in the last column position of the table.

The work table now looks like this:

![ Table 2 ](/Tables/Table2.png)

Now each line in the table is processed separately.






The decoder works according to the following principle:
- If the upper nibble is different from the binary value 1111, then the upper nibble is decoded.
- If the upper nibble has the binary value 1111, then the lower nibble is passed to the instruction decoder.

Determining whether the value of the upper nibble is 1111 in binary is done using a 4-input AND gate that generates the EXT signal.

Its output drives a 4-bit two-state multiplexer, which transmits the upper nibble if the EXT signal is low or the lower nibble if the EXT signal is high to the decoder.

The diagram of the described circuit made in the Logisim program is presented in figure 1.

![ Figure 1 ](/Pictures/Figure1.png)

The actual decoding of the instruction is done using a 32-bit decoder. 

The 4 bits that encode the instruction plus the EXT signal for encoding extended instructions are connected to the decoder's selection pins, which activate output pins O16 – O31.



# Implementation of the Control Unit using Combinational Logic Circuits
Making a circuit using Combinational Logic means using:
- You can standard logic
- Decoders
- Demultiplexers
- Multiplexers

## Logical equations
The first step consists in determining the logic equations for the implemented circuit.

For this purpose we use:
https://github.com/LincaMarius/ISAP-1_Computer_Instruction_Set

from where we will combine all the equations for the instructions we want to implement.

### Adding the NOP instruction
We start from the NOP instruction equations:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	NEXT = NOP * T3 + NOP * T4 + NOP * T5 + NOP * T6 + NOP * T7 + NOP * T8

The + sign signifies the logical OR operation.

The sign * stands for the logical AND operation.

### Adding the LDA instruction
We add the LDA instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + LDA * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3
-	DM = LDA * T4
-	LAH = LDA * T4
-	LAL = LDA * T4
-	NEXT = LDA * T5 + LDA * T6 + LDA * T7 + LDA * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3
-	DM = LDA * T4
-	LAH = LDA * T4
-	LAL = LDA * T4
-	NEXT = NOP * T3 + NOP * T4 + NOP * T5 + LDA * T5 + NOP * T6 + LDA * T6 + NOP * T7 + LDA * T7 + NOP * T8 + LDA * T8

### Adding the ADD instruction
We add the ADD instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + ADD * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = ADD * T3
-	DM = ADD * T4
-	LB = ADD * T4
-	EU = ADD * T5
-	LAH = ADD * T5
-	LAL = ADD * T5
-	NEXT = ADD * T6 + ADD * T7 + ADD * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3
-	DM = LDA * T4 + ADD * T4
-	LAH = LDA * T4 + ADD * T5
-	LAL = LDA * T4 + ADD * T5
-	LB = ADD * T4
-	EU = ADD * T5
-	NEXT = NOP * T3 + NOP * T4 + NOP * T5 + LDA * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + NOP * T7 + LDA * T7 + ADD * T7 + NOP * T8 + LDA * T8 + ADD * T8

### Adding the SUB instruction
-	EP = T1
-	LAR = T1 + SUB * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = SUB * T3
-	DM = SUB * T4
-	LB = SUB * T4
-	EU = SUB * T5
-	LAH = SUB * T5
-	LAL = SUB * T5
-	SU = SUB * T5
-	NEXT = SUB * T6 + SUB * T7 + SUB * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5
-	LAL = LDA * T4 + ADD * T5 + SUB * T5
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	NEXT = NOP * T3 + NOP * T4 + NOP * T5 + LDA * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8

### Adding the OUT instruction
We add the OUT instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + OUT * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = OUT * T3
-	EA = OUT * T4
-	I/O = OUT * T4
-	R/W = OUT * T4
-	NEXT = OUT * T5 + OUT * T6 + OUT * T7 + OUT * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5
-	LAL = LDA * T4 + ADD * T5 + SUB * T5
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	EA = OUT * T4
-	I/O = OUT * T4
-	R/W = OUT * T4
-	NEXT = NOP * T3 + NOP * T4 + NOP * T5 + LDA * T5 + OUT * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8

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
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5
-	LAL = LDA * T4 + ADD * T5 + SUB * T5
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	EA = OUT * T4
-	I/O = OUT * T4
-	R/W = OUT * T4
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + NOP * T5 + LDA * T5 + OUT * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8

### Adding the LIL instruction
We add the LIL instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LIL * T3
-	LAL = LIL * T3
-	NEXT = LIL * T4 + LIL * T5 + LIL * T6 + LIL * T7 + LIL * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	EA = OUT * T4
-	I/O = OUT * T4
-	R/W = OUT * T4
-	HLT = HLT * T3 + HLT * T4 + LIL * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8

### Adding the LIH instruction
We add the LIH instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LIH * T3
-	LAH = LIH * T3
-	NEXT = LIH * T4 + LIH * T5 + LIH * T6 + LIH * T7 + LIH * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	EA = OUT * T4
-	I/O = OUT * T4
-	R/W = OUT * T4
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8

### Adding the IN instruction
We add the IN instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + IN * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = IN * T3
-	I/O = IN * T4
-	LAH = IN * T4
-	LAL = IN * T4
-	NEXT = IN * T5 + IN * T6 + IN * T7 + IN * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	EA = OUT * T4
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8

### Adding the JMP instruction
We add the JMP instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = JMP * T3
-	LP = JMP * T3
-	NEXT = JMP * T4 + JMP * T5 + JMP * T6 + JMP * T7 + JMP * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	EA = OUT * T4
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4
-	LP = JMP * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8

### Adding the STA instruction
We add the STA instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + STA * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = STA * T3
-	EA = STA * T4
-	DM = STA * T4
-	R/W = STA * T4
-	NEXT = STA * T5 + STA * T6 + STA * T7 + STA * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5
-	EA = OUT * T4 + STA * T4
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8

### Adding the CMP instruction
We add the CMP instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = CMP * T3
-	DM = CMP * T4
-	LB = CMP * T4
-	SU = CMP * T5
-	NEXT = CMP * T6 + CMP * T7 + CMP * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4 + CMP * T4
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5 + CMP * T5
-	EA = OUT * T4 + STA * T4
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8

### Adding the CPY instruction
We add the CPY instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EA = CPY * T3
-	LB = CPY * T3
-	NEXT = CPY * T4 + CPY * T5 + CPY * T6 + CPY * T7 + CPY * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4 + CMP * T4 + CPY * T3
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5 + CMP * T5
-	EA = OUT * T4 + STA * T4 + CPY * T3
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8

### Adding the JZ instruction
We add the JZ instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = JZ * T3
-	LP = (JZ * Z) * T3
-	NEXT = JZ * T4 + JZ * T5 + JZ * T6 + JZ * T7 + JZ * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3 + JZ * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4 + CMP * T4 + CPY * T3
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5 + CMP * T5
-	EA = OUT * T4 + STA * T4 + CPY * T3
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3 + (JZ * Z) * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8

### Adding the JC instruction
We add the JC instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = JC * T3
-	LP = (JC * C) * T3
-	NEXT = JC * T4 + JC * T5 + JC * T6 + JC * T7 + JC * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3 + JZ * T3 + JC * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4 + CMP * T4 + CPY * T3
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5 + CMP * T5
-	EA = OUT * T4 + STA * T4 + CPY * T3
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3 + (JZ * Z) * T3 + (JC * C) * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + JC * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + JC * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + JC * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8 + JC * T8

### Adding the JS instruction
We add the JS instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = JS * T3
-	LP = (JS * S) * T3
-	NEXT = JS * T4 + JS * T5 + JS * T6 + JS * T7 + JS * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3 + JZ * T3 + JC * T3 + JS * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4
-	LB = ADD * T4 + SUB * T4 + CMP * T4 + CPY * T3
-	EU = ADD * T5 + SUB * T5
-	SU = SUB * T5 + CMP * T5
-	EA = OUT * T4 + STA * T4 + CPY * T3
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3 + (JZ * Z) * T3 + (JC * C) * T3 + (JS * S) * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + JS * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + JC * T5 + JS * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + JC * T6 + JS * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + JC * T7 + JS * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8 + JC * T8 + JS * T8

### Adding the INC instruction
We add the INC instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EC = INC * T3
-	LB = INC * T3
-	SC0 = INC * T3
-	EU = INC * T4
-	LAH = INC * T4
-	LAL = INC * T4
-	NEXT = INC * T5 + INC * T6 + INC * T7 + INC * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3 + JZ * T3 + JC * T3 + JS * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4 + INC * T4
-	LB = ADD * T4 + SUB * T4 + CMP * T4 + CPY * T3 + INC * T3
-	EU = ADD * T5 + SUB * T5 + INC * T4
-	SU = SUB * T5 + CMP * T5
-	EA = OUT * T4 + STA * T4 + CPY * T3
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3 + (JZ * Z) * T3 + (JC * C) * T3 + (JS * S) * T3
-	EC = INC * T3
-	SC1 = INC * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + JS * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + JC * T5 + JS * T5 + INC * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + JC * T6 + JS * T6 + INC * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + JC * T7 + JS * T7 + INC * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8 + JC * T8 + JS * T8 + INC * T8

### Adding the DEC instruction
We add the DEC instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EC = DEC * T3
-	LB = DEC * T3
-	SC0 = DEC * T3
-	EU = DEC * T4
-	LAH = DEC * T4
-	LAL = DEC * T4
-	SU = DEC * T4
-	NEXT = DEC * T5 + DEC * T6 + DEC * T7 + DEC * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3 + JZ * T3 + JC * T3 + JS * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4 + DEC * T4
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4 + INC * T4 + DEC * T4
-	LB = ADD * T4 + SUB * T4 + CMP * T4 + CPY * T3 + INC * T3 + DEC * T3
-	EU = ADD * T5 + SUB * T5 + INC * T4 + DEC * T4
-	SU = SUB * T5 + CMP * T5 + DEC * T4
-	EA = OUT * T4 + STA * T4 + CPY * T3
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3 + (JZ * Z) * T3 + (JC * C) * T3 + (JS * S) * T3
-	EC = INC * T3 + DEC * T3
-	SC1 = INC * T3 + DEC * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + JS * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + JC * T5 + JS * T5 + INC * T5 + DEC * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + JC * T6 + JS * T6 + INC * T6 + DEC * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + JC * T7 + JS * T7 + INC * T7 + DEC * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8 + JC * T8 + JS * T8 + INC * T8 + DEC * T8

### Adding the NEG instruction
We add the NEG instruction that has the following boolean equations for the signals that are active when this instruction is executed:
-	EP = T1
-	LAR = T1
-	PM = T2
-	LI = T2
-	CP = T2
-	EA = NEG * T3
-	LB = NEG * T3
-	EC = NEG * T4
-	LAH = NEG * T4
-	LAL = NEG * T4
-	EU = NEG * T5
-	LAH = NEG * T5
-	LAL = NEG * T5
-	SU = NEG * T5
-	NEXT = NEG * T6 + NEG * T7 + NEG * T8

By combining these equations with the previous ones we obtain the following Boolean equations:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3 + JZ * T3 + JC * T3 + JS * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LDA * T4 + ADD * T5 + SUB * T5 + LIH * T3 + IN * T4 + DEC * T4 + NEG * T4 + NEG * T5
-	LAL = LDA * T4 + ADD * T5 + SUB * T5 + LIL * T3 + IN * T4 + INC * T4 + DEC * T4 + NEG * T4 + NEG * T5
-	LB = ADD * T4 + SUB * T4 + CMP * T4 + CPY * T3 + INC * T3 + DEC * T3 + NEG * T3
-	EU = ADD * T5 + SUB * T5 + INC * T4 + DEC * T4 + NEG * T5
-	SU = SUB * T5 + CMP * T5 + DEC * T4 + NEG * T5
-	EA = OUT * T4 + STA * T4 + CPY * T3 + NEG * T3
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3 + (JZ * Z) * T3 + (JC * C) * T3 + (JS * S) * T3
-	EC = INC * T3 + DEC * T3 + NEG * T4
-	SC1 = INC * T3 + DEC * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + JS * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + JC * T5 + JS * T5 + INC * T5 + DEC * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + JC * T6 + JS * T6 + INC * T6 + DEC * T6 + NEG * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + JC * T7 + JS * T7 + INC * T7 + DEC * T7 + NEG * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8 + JC * T8 + JS * T8 + INC * T8 + DEC * T8 + NEG * T8

## Optimizing equations
If we look at the final equations we can easily conclude that we will need many logic gates to implement this circuit.

### Ordering the terms of the equations
Before doing anything else it is necessary to order the terms that make up the above equations.

I ordered the equations in ascending order of T elements:
-	EP = T1
-	LAR = T1 + LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + IN * T3 + STA * T3 + CMP * T3
-	PM = T2
-	LI = T2
-	CP = T2
-	EI = LDA * T3 + ADD * T3 + SUB * T3 + OUT * T3 + LIL * T3 + LIH * T3 + IN * T3 + JMP * T3 + STA * T3 + CMP * T3 + JZ * T3 + JC * T3 + JS * T3
-	DM = LDA * T4 + ADD * T4 + SUB * T4 + STA * T4 + CMP * T4
-	LAH = LIH * T3 + LDA * T4 + IN * T4 + DEC * T4 + NEG * T4 + ADD * T5 + SUB * T5 + NEG * T5
-	LAL = LIL * T3 + LDA * T4 + IN * T4 + INC * T4 + DEC * T4 + NEG * T4 + ADD * T5 + SUB * T5 + NEG * T5
-	LB = CPY * T3 + INC * T3 + DEC * T3 + NEG * T3 + ADD * T4 + SUB * T4 + CMP * T4
-	EU = INC * T4 + DEC * T4 + ADD * T5 + SUB * T5 + NEG * T5
-	SU = DEC * T4 + SUB * T5 + CMP * T5 + NEG * T5
-	EA = CPY * T3 + NEG * T3 + OUT * T4 + STA * T4
-	I/O = OUT * T4 + IN * T4
-	R/W = OUT * T4 + STA * T4
-	LP = JMP * T3 + Z * JZ * T3 + C * JC * T3 + S * JS * T3
-	EC = INC * T3 + DEC * T3 + NEG * T4
-	SC1 = INC * T3 + DEC * T3
-	HLT = HLT * T3 + HLT * T4 + HLT * T5 + HLT * T6 + HLT * T7 + HLT * T8
-	NEXT = NOP * T3 + NOP * T4 + LIL * T4 + LIH * T4 + JMP * T4 + CPY * T4 + JZ * T4 + JC * T4 + JS * T4 + NOP * T5 + LDA * T5 + OUT * T5 + LIL * T5 + LIH * T5 + IN * T5 + JMP * T5 + STA * T5 + CPY * T5 + JZ * T5 + JC * T5 + JS * T5 + INC * T5 + DEC * T5 + NOP * T6 + LDA * T6 +  ADD * T6 + SUB * T6 + OUT * T6 + LIL * T6 + LIH * T6 + IN * T6 + JMP * T6 + STA * T6 + CMP * T6 + CPY * T6 + JZ * T6 + JC * T6 + JS * T6 + INC * T6 + DEC * T6 + NEG * T6 + NOP * T7 + LDA * T7 + ADD * T7 + SUB * T7 + OUT * T7 + LIL * T7 + LIH * T7 + IN * T7 + JMP * T7 + STA * T7 + CMP * T7 + CPY * T7 + JZ * T7 + JC * T7 + JS * T7 + INC * T7 + DEC * T7 + NEG * T7 + NOP * T8 + LDA * T8 + ADD * T8 + SUB * T8 + OUT * T8 + LIL * T8 + LIH * T8 + IN * T8 + JMP * T8 + STA * T8 + CMP * T8 + CPY * T8 + JZ * T8 + JC * T8 + JS * T8 + INC * T8 + DEC * T8 + NEG * T8

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


