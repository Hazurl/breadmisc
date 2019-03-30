# BreadMISC Architecture Specification

This document describes the specification for the BreadMISC architecture.

## Overview

BreadMISC is an instruction set architecture designed for creating an architecture with a minimal yet understandable set of instructions that fit within a single word size. This architecture is designed with the belief that a smaller number of instructions leads to a faster processing speed. Using a smaller number of instructions will reduce the number of transistors used. This means that the data will be processed faster while creating less heat. In theory, if a BreadMISC processor can calculate the product of two numbers just as fast as an x86 processor would be able to, then BreadMISC will be faster overall. This is because it has executed several instructions in the time it took the x86 processor to execute a single instruction. By eliminating complex instructions such as multiplication, we have eliminated the wait that would normally have to occur in order to let other the more complex instructions execute. Now let's return to the previous scenario where the BreadMISC processor had the same multiplication speed as the x86 processor. If we wanted to simply add two numbers together, the x86 processor would have to wait while the BreadMISC processor has already finished calculating the sum.

## System Specification

| Property                   | 16-bit  | 32-bit |
| -------------------------- | ------- | ------ |
| Maximum Addressable Memory | 128 KiB | 16 GiB |
| Maximum Peripherals        | 8       | ?      |
| Register Count             | 4       | ?      |

## List of instructions

| Mnemonic | Instruction Name  | Instruction Code | Description                                                                                 |
| -------- | ----------------- | ---------------- | ------------------------------------------------------------------------------------------- |
| add      | Add               | 0                | Adds the values of two registers together                                                   |
| mov      | Move              | 1                | Moves the value of something to/from a register                                             |
| bop      | Bitwise Operation | 2                | Performs a bitwise operation on r0 using a truth table and the value of a register          |
| aux      | Auxiliary         | 3                | Sends data from a register to a peripheral or receives data from a peripheral to a register |
| bsh      | Bitwise Shift     | 4                | Performs a bitwise shift operation on a register                                            |
| wdr      | Watchdog Reset    | 5                | Resets the watchdog timer to prevent the system from rebooting                              |

## List of pseudo instructions

These are instructions that don't exist but can be recreated using the existing instructions.

| Mnemonic | Instruction Name    | Equivalent Instruction | Note                                                                     |
| -------- | ------------------- | ---------------------- | ------------------------------------------------------------------------ |
| and      | AND                 | bop                    | Using truth table 8                                                      |
| jmp      | Jump to Instruction | mov                    | Move the address to rp                                                   |
| lsh      | Left Bitwise Shift  | bsh                    | The bitwise shift instruction does exactly this(Direction flag 0)        |
| nand     | NOT AND             | bop                    | Using truth table 7                                                      |
| nor      | NOT OR              | bop                    | Using truth table 1                                                      |
| not      | NOT                 | bop                    | Using truth table 3 or 5(depending on which bit you want to flip)        |
| or       | OR                  | bop                    | Using truth table 14                                                     |
| rsh      | Right Bitwise Shift | bsh                    | The bitwise shift instruction does exactly this(Direction flag 1)        |
| sub      | Subtract            | add                    | Adding a negative number works the same as subtracting a positive number |
| xnor     | Exclusive NOT OR    | bop                    | Using truth table 5                                                      |
| xor      | Exclusive OR        | bop                    | Using truth table 6                                                      |

## List of registers

| Mnemonic | Register Name    | 16-bit Code | 32-bit Code | Description              |
| -------- | ---------------- | ----------- | ----------- | ------------------------ |
| r0       | Register 0       | 0           | 0           | General Purpose Register |
| r1       | Register 1       | 1           | 1           | General Purpose Register |
| r2       | Register 2       | 2           | 2           | General Purpose Register |
| rp       | Pointer Register | 3           | ?           | Instruction Pointer      |

## Instruction Encoding

### 16-bit

| Field Name           | Size | Position | Description                                                                       |
| -------------------- | ---- | -------- | --------------------------------------------------------------------------------- |
| Condition Register A | 2b   | 0        | The first register to be used in condition checking                               |
| Condition Register B | 2b   | 2        | The second register to be used in condition checking                              |
| Condition Operation  | 3b   | 4        | The condition operator to use to determine whether the instruction should execute |
| Instruction Code     | 3b   | 7        | The instruction to be executed                                                    |
| Instruction Specific | 6b   | 10       | Instruction-specific data                                                         |

### 32-bit

TODO

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

TODO

#### Notes

If the immediate flag is set then the next word read from memory is the number to be added to the register.

### Auxiliary

#### Instruction Specific Data

##### 16-bit

| Field Name | Size | Position | Description                                    |
| ---------- | ---- | -------- | ---------------------------------------------- |
| Read/Write | 1b   | 10       | Whether the operation writes to the peripheral |
| Register   | 2b   | 11       | The register to read from or write to          |
| Peripheral | 3b   | 13       | The peripheral to read from or write to        |

##### 32-bit

TODO

### Bitwise Operation

#### Instruction Specific Data

##### 16-bit

| Field Name  | Size | Position | Description                                   |
| ----------- | ---- | -------- | --------------------------------------------- |
| Register B  | 2b   | 10       | The register to get the second value from     |
| Truth Table | 4b   | 12       | The truth table to use to process the numbers |

##### 32-bit

TODO

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

TODO

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

TODO

#### Notes

If the immediate flag is set then the next word read from memory is the number to be added to the register.

## Calling Convention

TODO
