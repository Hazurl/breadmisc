# BreadMISC Instruction Format

This document describes the specification for the BreadMISC architecture.

## Overview

BreadMISC is a 16/32-bit instruction set architecture designed for creating an architecture with a minimal yet understandable set of instructions that fit within a single word size. This architecture is designed with the belief that a smaller number of instructions leads to a faster processing speed. Using a smaller number of instructions will reduce the number of transistors used. This means that the data will be processed faster while creating less heat. In theory, if a BreadMISC processor can calculate the product of two numbers just as fast as an x86 processor would be able to, then BreadMISC will be faster overall. This is because it has executed several instructions in the time it took the x86 processor to execute a single instruction. In today's growing world of GHz speeds, we don't need a dedicated multiplication instruction. By eliminating complex instructions such as multiplication, we have eliminated the wait that would normally have to occur in order to let other the more complex instructions execute. Now let's return to the previous scenario where the BreadMISC processor had the same multiplication speed as the x86 processor. If we wanted to simply add two numbers together, the x86 processor would have to wait while the BreadMISC processor has already finished calculating the sum.

## System Specification

TODO

## List of instructions

TODO

## List of pseudo instructions

TODO

## List of registers

TODO

## Instruction Encoding

TODO

## Instruction-Specific Documentation

TODO
