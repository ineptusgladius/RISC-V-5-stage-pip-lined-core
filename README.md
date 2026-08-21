# 5-Stage Pipelined CPU Core

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Language](https://img.shields.io/badge/language-[Verilog%20%2F%20VHDL]-orange)

A fully functional, 32-bit **[RISC-V / MIPS]** microprocessor core featuring a classic 5-stage instruction pipeline. This project implements fundamental computer architecture concepts, including hazard detection, data forwarding, and branch prediction to maximize instruction throughput.

##  Features

*   **Architecture:** 32-bit **[RISC-V (RV32I) / MIPS]** Instruction Set Architecture.
*   **Pipeline Stages:** 5-stage classic pipeline (IF, ID, EX, MEM, WB).
*   **Hazard Handling:** 
    *   Data Hazard resolution via Forwarding Unit (Bypassing).
    *   Load-Use Hazard resolution via Pipeline Stalling / Hazard Detection Unit.
    *   Control Hazard handling via **[Branch Flushing / Static Branch Prediction]**.
*   **Memory Interface:** Separate Instruction and Data memories (Harvard Architecture).
*   **Synthesizable:** Written in standard **[Verilog-2001 / VHDL / SystemVerilog]** and ready for FPGA deployment.

##  Architecture Overview

The CPU executes instructions across five independent stages to achieve a theoretical Instructions Per Cycle (IPC) of 1:

1.  **Instruction Fetch (IF):** Retrieves the next instruction from Instruction Memory using the Program Counter (PC) and increments the PC.
2.  **Instruction Decode (ID):** Decodes the fetched instruction, reads operands from the Register File, and generates control signals.
3.  **Execute (EX):** The Arithmetic Logic Unit (ALU) performs mathematical/logical operations, calculates branch target addresses, and handles data forwarding.
4.  **Memory Access (MEM):** Reads from or writes to the Data Memory for load/store instructions.
5.  **Write Back (WB):** Writes ALU results or memory read data back to the destination register in the Register File.
<img width="977" height="563" alt="117547053-f932fe00-b046-11eb-91af-9291291d4f52" src="https://github.com/user-attachments/assets/8bdc3086-c50a-4564-b8f6-0927410c59ea" />


