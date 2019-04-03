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

| Mnemonic | Register Name    | 16-bit Code | 32-bit Code | Description               |
| -------- | ---------------- | ----------- | ----------- | ------------------------- |
| r0 - r2  | Registers 0 - 2  | 0 - 2       | 0 - 2       | General Purpose Registers |
| r3 - r30 | Registers 3 - 30 | N/A         | 3 - 30      | General Purpose Registers |
| rp       | Pointer Register | 3           | 31          | Instruction Pointer       |

## Instruction Encoding

### 16-bit

| Field Name           | Size | Position | Description                                                                       |
| -------------------- | ---- | -------- | --------------------------------------------------------------------------------- |
| High Level Bit       | 1b   | 0        | Whether the instruction is designed for a processor with more than 16 bits        |
| Condition Register A | 2b   | 1        | The first register to be used in condition checking                               |
| Condition Register B | 2b   | 3        | The second register to be used in condition checking                              |
| Condition Operation  | 3b   | 5        | The condition operator to use to determine whether the instruction should execute |
| Instruction Code     | 2b   | 8        | The instruction to be executed                                                    |
| Instruction Specific | 6b   | 10       | Instruction-specific data(See section "Instruction-Specific Documentation")       |

### 32-bit

| Field Name           | Size | Position | Description                                                                       |
| -------------------- | ---- | -------- | --------------------------------------------------------------------------------- |
| High Level Bit       | 1b   | 0        | Whether the instruction is designed for a processor with more than 16 bits        |
| Instruction Width    | 4b   | 1        | The word size of the operation(2^(5+n) bit)                                       |
| Condition Register A | 5b   | 5        | The first register to be used in condition checking                               |
| Condition Register B | 5b   | 10       | The second register to be used in condition checking                              |
| Condition Operation  | 3b   | 15       | The condition operator to use to determine whether the instruction should execute |
| Instruction Code     | 2b   | 18       | The instruction to be executed                                                    |
| Instruction Specific | 12b  | 20       | Instruction-specific data(See section "Instruction-Specific Documentation")       |

### Condition Operations

| Operation       | Symbol | Code |
| --------------- | ------ | ---- |
| Always          | N/A    | 000  |
| Equal to        | =      | 001  |
| Greater than    | >      | 010  |
| Less than       | <      | 011  |
| Never           | N/A    | 100  |
| Not equal to    | !=     | 101  |
| Not grater than | <=     | 110  |
| Not less than   | >=     | 111  |

#### Note

Think of the first four operations as the actual operations and the most significant bit as the negation flag.

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

| Field Name | Size | Position | Description                                                                    |
| ---------- | ---- | -------- | ------------------------------------------------------------------------------ |
| Immediate  | 1b   | 20       | Whether the operation adds an immediate or a value of a register to a register |
| Register A | 5b   | 21       | The source register to add to                                                  |
| Register B | 5b   | 26       | The register to add to the source register                                     |

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

| Field Name  | Size | Position | Description                                   |
| ----------- | ---- | -------- | --------------------------------------------- |
| Register B  | 5b   | 20       | The register to get the second value from     |
| Truth Table | 4b   | 25       | The truth table to use to process the numbers |

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

| Field Name | Size | Position | Description                                                                   |
| ---------- | ---- | -------- | ----------------------------------------------------------------------------- |
| Direction  | 1b   | 20       | The direction of the bitwise shift                                            |
| Immediate  | 1b   | 21       | Whether an immediate is used instead of register B                            |
| Register A | 5b   | 22       | The register which contains the value to be shifted and will store the result |
| Register B | 5b   | 27       | The register which contains the number of bits to shift                       |


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

| Field Name      | Size | Position | Description                                                                                                                                    |
| --------------- | ---- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Memory          | 1b   | 20       | Whether memory is used instead of a register or an immediate                                                                                   |
| Write/Immediate | 1b   | 21       | If the last bit is set, this bit determines whether to write to memory. Otherwise, it determines whether this operation will use an immediate. |
| Register A      | 5b   | 22       | The register to move data to                                                                                                                   |
| Register B      | 5b   | 27       | The register to move data from                                                                                                                 |

#### Notes

If the immediate flag is set then the next word read from memory is the number to be added to the register. If the memory flag is set then the next word read from memory is the pointer to the data to be read/written to.

## Calling Convention

TODO

## Memory Map

TODO
