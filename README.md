# ISAP-1 Computer
This is my step-by-step process of designing the ISAP-1 Computer Control Unit block.

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

## The Main Instruction Set of the ISAP-1 computer is:

| Mnemonic | Opcode | Hex | Steps | Description                                                   |
|----------|--------|-----|-------|---------------------------------------------------------------|
| LDA      | 0000   | 0h  |   4   | Load RAM data into Accumulator                                |
| ADD      | 0001   | 1h  |   5   | Add RAM data to Accumulator                                   |
| SUB      | 0010   | 2h  |   5   | Substract RAM data from accumulator                           |
| LIL      | 0011   | 3h  |   3   | Load immediate value into the lower nibble of the Accumulator |
| LIH      | 0100   | 4h  |   3   | Load immediate value into the upper nibble of the Accumulator |
| STA      | 0101   | 5h  |   4   | Store Accumulator data into RAM                               |
| CMP      | 0110   | 6h  |   5   | Compare RAM data with Accumulator                             |
| JZ       | 1001   | 9h  |   3   | Jump if Zero flag is set                                      |
| JC       | 1010   | Ah  |   3   | Jump if Carry flag is set                                     |
| JS       | 1011   | Bh  |   3   | Jump if Sign flag is set                                      |
| JMP      | 1100   | Ch  |   3   | Unconditional jump to address n                               |
| IN       | 1101   | Dh  |   4   | Input data from Port p into the Accumulator                   |
| OUT      | 1110   | Eh  |   4   | Load Accumulator data into Output Register                    |
| -        | 1111   | Fh  |   -   | Prefix for extended instruction set                           |

In total, 13 out of 15 possible instructions are implemented.

## The Extended Instruction Set of the ISAP-1 computer is:

| Mnemonic | Opcode    | Hex | Steps | Operation                                 |
|----------|-----------|-----|-------|-------------------------------------------|
| NOP      | 1111 0000 | F0h |   2   | No Operation                              |
| INC      | 1111 0001 | F1h |   4   | Increment the contents of the Accumulator |
| DEC      | 1111 0010 | F2h |   4   | Decrement the contents of the Accumulator |
| NEG      | 1111 0011 | F3h |   5   | Negate Accumulator                        |
| CPY      | 1111 1110 | FEh |   3   | Copy Accumulator data into B register     |
| HLT      | 1111 1111 | FFh |   2   | Stop processing                           |

In total, 6 extended instructions out of 16 possible are implemented.

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




