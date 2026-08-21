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
## Implementation
<img width="977" height="563" alt="117547053-f932fe00-b046-11eb-91af-9291291d4f52" src="https://github.com/user-attachments/assets/8bdc3086-c50a-4564-b8f6-0927410c59ea" />
##  Hazard Detection and Control Unit

This module acts as the "traffic controller" for the 5-stage pipelined CPU. It monitors the instructions flowing through the pipeline and detects situations where the pipeline must be paused (stalled) or cleared (flushed) to ensure correct program execution.

##  Overview

In a classic 5-stage pipeline, instructions overlap. This overlap can cause **hazards** when an instruction depends on the results of a previous instruction that hasn't finished yet, or when the flow of the program changes abruptly. 

This unit is responsible for handling two main types of hazards:
1. **Load-Use Data Hazards:** Handled via Pipeline Stalling.
2. **Control Hazards (Branches/Jumps):** Handled via Pipeline Flushing.

*(Note: Standard data hazards are handled by a separate Data Forwarding Unit, which routes data directly between pipeline registers without stalling).*

##  Hazards Handled

### 1. Load-Use Hazard (Stalling)
A load-use hazard occurs when an instruction needs to read a register that is currently being loaded from memory by the immediately preceding instruction. Because the data isn't available from memory until the `MEM` stage, forwarding cannot solve this.

*   **Detection Condition:** The instruction in the Execute (EX) stage is a memory read (e.g., `lw`), AND its destination register matches either of the source registers of the instruction in the Decode (ID) stage.
*   **Action Taken:** 
    *   `Stall_IF`: Freezes the Program Counter (PC) so the same instruction is fetched again.
    *   `Stall_ID`: Freezes the IF/ID pipeline register so the decoded instruction isn't lost.
    *   `Flush_EX`: Inserts a "bubble" (a NOP - No Operation) into the ID/EX pipeline register to let the load instruction finish.

### 2. Control Hazard (Flushing)
Control hazards occur when a branch (e.g., `beq`) or jump (e.g., `j`) instruction is executed. The pipeline has already fetched the next sequential instructions, but if the branch is taken, those fetched instructions are incorrect.

*   **Detection Condition:** A branch or jump is evaluated as "taken" by the ALU or Decode unit.
*   **Action Taken:**
    *   `Flush_IF`: Clears the IF/ID pipeline register, discarding the incorrectly fetched instruction.
    *   `Flush_ID` *(if branch is resolved in EX)*: Clears the ID/EX pipeline register as well.


