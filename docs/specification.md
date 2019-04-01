# BreadMISC Architecture Specification

This document describes the specification for the BreadMISC architecture.

## Overview

BreadMISC is an instruction set architecture designed for creating an architecture with a minimal yet understandable set of instructions that fit within a single word size. This architecture is designed with the belief that a smaller number of instructions leads to a faster processing speed. Using a smaller number of instructions will reduce the number of transistors used. This means that the data will be processed faster while creating less heat. In theory, if a BreadMISC processor can calculate the product of two numbers just as fast as an x86 processor would be able to, then BreadMISC will be faster overall. This is because it has executed several instructions in the time it took the x86 processor to execute a single instruction. By eliminating complex instructions such as multiplication, we have eliminated the wait that would normally have to occur in order to let other the more complex instructions execute. Now let's return to the previous scenario where the BreadMISC processor had the same multiplication speed as the x86 processor. If we wanted to simply add two numbers together, the x86 processor would have to wait while the BreadMISC processor has already finished calculating the sum.

## System Specification

| Property                   | 16-bit  | 32-bit | 64-bit  | 128-bit | 256-bit |
| -------------------------- | ------- | ------ | ------- | ------- | ------- |
| Maximum Addressable Memory | 128 KiB | 16 GiB | 128 EiB | ?       | ?       |
| Register Count             | 4       | 16     | ?       | ?       | ?       |

## List of instructions

| Mnemonic | Instruction Name  | Instruction Code | Description                                                                                 |
| -------- | ----------------- | ---------------- | ------------------------------------------------------------------------------------------- |
| add      | Add               | 0                | Adds the values of two registers together                                                   |
| mov      | Move              | 1                | Moves the value of something to/from a register                                             |
| bop      | Bitwise Operation | 2                | Performs a bitwise operation on r0 using a truth table and the value of a register          |
| bsh      | Bitwise Shift     | 3                | Performs a bitwise shift operation on a register                                            |

## List of pseudo instructions

These are instructions that don't exist but can be recreated using the existing instructions.

| Mnemonic | Instruction Name    | Equivalent Instruction | Note                                                                                            |
| -------- | ------------------- | ---------------------- | ----------------------------------------------------------------------------------------------- |
| and      | AND                 | bop                    | Using truth table 8                                                                             |
| cmp      | Compare             | N/A                    | All instructions are encoded with a comparison statement to determine whether it should execute |
| jmp      | Jump to Instruction | mov                    | Move the address to rp                                                                          |
| lsh      | Left Bitwise Shift  | bsh                    | The bitwise shift instruction does exactly this(Direction flag 0)                               |
| nand     | NOT AND             | bop                    | Using truth table 7                                                                             |
| nor      | NOT OR              | bop                    | Using truth table 1                                                                             |
| not      | NOT                 | bop                    | Using truth table 3 or 5(depending on which bit you want to flip)                               |
| or       | OR                  | bop                    | Using truth table 14                                                                            |
| rsh      | Right Bitwise Shift | bsh                    | The bitwise shift instruction does exactly this(Direction flag 1)                               |
| sub      | Subtract            | add                    | Adding a negative number works the same as subtracting a positive number                        |
| xnor     | Exclusive NOT OR    | bop                    | Using truth table 5                                                                             |
| xor      | Exclusive OR        | bop                    | Using truth table 6                                                                             |

## List of registers

| Mnemonic | Register Name    | 16-bit Code | 32-bit Code | Description              |
| -------- | ---------------- | ----------- | ----------- | ------------------------ |
| r0       | Register 0       | 0           | 0           | General Purpose Register |
| r1       | Register 1       | 1           | 1           | General Purpose Register |
| r2       | Register 2       | 2           | 2           | General Purpose Register |
| r3       | Register 3       | N/A         | 3           | General Purpose Register |
| r4       | Register 4       | N/A         | 4           | General Purpose Register |
| r5       | Register 5       | N/A         | 5           | General Purpose Register |
| r6       | Register 6       | N/A         | 6           | General Purpose Register |
| r7       | Register 7       | N/A         | 7           | General Purpose Register |
| r8       | Register 8       | N/A         | 8           | General Purpose Register |
| r9       | Register 9       | N/A         | 9           | General Purpose Register |
| r10      | Register 10      | N/A         | 10          | General Purpose Register |
| r11      | Register 11      | N/A         | 11          | General Purpose Register |
| r12      | Register 12      | N/A         | 12          | General Purpose Register |
| r13      | Register 13      | N/A         | 13          | General Purpose Register |
| r14      | Register 14      | N/A         | 14          | General Purpose Register |
| rp       | Pointer Register | 3           | 15          | Instruction Pointer      |

## Instruction Encoding

### 16-bit

| Field Name           | Size | Position | Description                                                                       |
| -------------------- | ---- | -------- | --------------------------------------------------------------------------------- |
| Condition Register A | 2b   | 0        | The first register to be used in condition checking                               |
| Condition Register B | 2b   | 2        | The second register to be used in condition checking                              |
| Condition Operation  | 3b   | 4        | The condition operator to use to determine whether the instruction should execute |
| Instruction Code     | 2b   | 7        | The instruction to be executed                                                    |
| High Level Bit       | 1b   | 9        | Whether the instruction is designed for a processor with more than 16 bits        |
| Instruction Specific | 6b   | 10       | Instruction-specific data                                                         |

### 32-bit

In addition to the 16-bit fields, 32-bit adds additional data for word lengths beyond 16-bit as well as additional registers for general purpose use.

| Field Name                  | Size | Position | Description                                                       |
| --------------------------- | ---- | -------- | ----------------------------------------------------------------- |
| Word Size                   | 2b   | 16       | Determines the word size of the instruction(2^(5+n) bits)         |
| Conditional Register Bank A | 2b   | 18       | Determines the register bank to get Conditional Register A from   |
| Conditional Register Bank B | 2b   | 20       | Determines the register bank to get Conditional Register B from   |
| Source Register Bank        | 2b   | 22       | Determines the register bank to get the Source Register from      |
| Destination Register Bank   | 2b   | 24       | Determines the register bank to get the Destination Register from |
| Reserved                    | 6b   | 26       | Reserved for future use                                           |

#### Note

Register banks are really just the upper two bits of the register code.

## Instruction-Specific Documentation

### Add

#### Instruction Specific Data

##### 16-bit

| Field Name | Size | Position | Description                                                                    |
| ---------- | ---- | -------- | ------------------------------------------------------------------------------ |
| Immediate  | 1b   | 10       | Whether the operation adds an immediate or a value of a register to a register |
| Register A | 2b   | 11       | The source register to add to                                                  |
| Register B | 2b   | 13       | The register to add to the source register                                     |

##### 32-bit

BreadMISC32 does not add any additional data to this instruction.

#### Notes

If the immediate flag is set then the next word read from memory is the number to be added to the register.

### Bitwise Operation

#### Instruction Specific Data

##### 16-bit

| Field Name  | Size | Position | Description                                   |
| ----------- | ---- | -------- | --------------------------------------------- |
| Register B  | 2b   | 10       | The register to get the second value from     |
| Truth Table | 4b   | 12       | The truth table to use to process the numbers |

##### 32-bit

BreadMISC32 does not add any additional data to this instruction.

#### Encoding Truth Tables

Encoding truth tables for BreadMISC is relatively easy to do. First of all, you need an actual truth table. Here's an example truth table for XOR:

| A | B | A ^ B |
| - | - | ----- |
| 0 | 0 | 0     |
| 0 | 1 | 1     |
| 1 | 0 | 1     |
| 1 | 1 | 0     |

Using the truth table, we need to encode the result in binary. To do this, we simply take the first position(where A = 0 and B = 0) and make that the first bit(ones place) of our encoded truth table. Then we take the second position(where A = 0 and B = 1) and make it the second bit(twos place). If you keep following that pattern, you should end up with the encoded truth table 6. You can use this process to find all 16 logic gates.

### Bitwise Shift

#### Instruction Specific Data

##### 16-bit

| Field Name | Size | Position | Description                                                                   |
| ---------- | ---- | -------- | ----------------------------------------------------------------------------- |
| Direction  | 1b   | 10       | The direction of the bitwise shift                                            |
| Immediate  | 1b   | 11       | Whether an immediate is used instead of register B                            |
| Register A | 2b   | 12       | The register which contains the value to be shifted and will store the result |
| Register B | 2b   | 14       | The register which contains the number of bits to shift                       |

##### 32-bit

BreadMISC32 does not add any additional data to this instruction.

#### Notes

If the immediate flag is set then the next word read from memory is the number to be added to the register.

### Move

#### Instruction Specific Data

##### 16-bit

| Field Name      | Size | Position | Description                                                                                                                                    |
| --------------- | ---- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Memory          | 1b   | 10       | Whether memory is used instead of a register or an immediate                                                                                   |
| Write/Immediate | 1b   | 11       | If the last bit is set, this bit determines whether to write to memory. Otherwise, it determines whether this operation will use an immediate. |
| Register A      | 2b   | 12       | The register to move data to                                                                                                                   |
| Register B      | 2b   | 14       | The register to move data from                                                                                                                 |

##### 32-bit

BreadMISC32 does not add any additional data to this instruction.

#### Notes

If the immediate flag is set then the next word read from memory is the number to be added to the register. If the memory flag is set then the next word read from memory is the pointer to the data to be read/written to.

## Calling Convention

TODO

## Memory Map

TODO
