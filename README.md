# 8-bit FSM CPU with 5-Stage Pipelining and Hardware Interaction

### Authors
- 2021742027 Hwapyung Kim
- 2020742022 Junsoo Moon
- 2021742038 Wonho Lee

---

## Contents

1. Abstract
2. Introduction
   - 2-1. Single-cycle, Multi-cycle and Pipeline CPU
   - 2-2. Hazards in Pipeline CPU
3. Starting Point
4. Improved Process
5. Limitations and Practical Constraints
6. Results
7. Expected Outcomes

---

## 1. Abstract

This project explores the transformation of an FSM-based 8-bit CPU into a pipelined architecture, implementing a classic 5-stage RISC-style pipeline: IF (Instruction Fetch), ID (Instruction Decode), EX (Execute), MEM (Memory Access), and WB (Write Back). Designed in Verilog HDL, each stage was modularized to reflect real CPU architecture. The CPU was deployed on a Cyclone V FPGA board (Terasic DE10-Standard), where hardware-level interaction is enabled via switches and buttons for ROM, RAM, and Register control.

This design allows users to not only execute instructions but also actively interact with memory components by inputting or modifying data using physical switches, observing execution through LEDs and 7-segment displays. Initially aimed at improving performance through pipelining, the project also evolved into an educational tool for learning pipeline architecture, addressing hazards, synchronization, and performance bottlenecks.

---

## 2. Introduction

Modern CPUs use advanced structural designs to enhance execution speed and resource efficiency. Among the most fundamental design methodologies are:
- **Single-cycle**: Each instruction completes in one clock cycle.
- **Multi-cycle (FSM-based)**: Instructions are broken down into multiple clock cycles for flexibility.
- **Pipelined**: Stages of instruction execution are overlapped to improve throughput.

This project discusses the conceptual differences between these architectures and the specific hazards (data, control, structural) that arise in pipelined designs.

---

## 3. Starting Point

The base design was an open-source 8-bit FSM-controlled RISC CPU by Qi Liu (Tsinghua University). The selection was based on:
- Simplicity and clarity for learning.
- RISC principles for efficient pipelining.
- Clean, modular Verilog code.

The instruction set includes long and short type instructions such as LOD, LDA, STO (data transfer), PRE, ADD, LDM, HLT, NOP (control/ALU). A simple ROM test program loads numbers, adds them, stores results, and halts.

---

## 4. Improved Process

### Stage 1: Basic 3-Stage Pipeline
The initial step involved creating a 3-stage pipeline (IF → ID → EX) from the FSM architecture. Modules like `rom`, `counter`, and `addr_mux` were unified under the IF stage.

### Stage 2: Expansion to 5-Stage Pipeline
MEM and WB stages were added, following the conventional RISC layout. Each pipeline register (`IF_ID`, `ID_EX`, `EX_MEM`, `MEM_WB`) was modularized to isolate signals across stages. Forwarding logic was introduced for data hazard resolution.

### Stage 3: FPGA Integration
Using switches and buttons:
- Users can write values to RAM via toggles.
- ROM, RAM, or Register contents can be selected and read.
- Address/data visibility is provided via 7-segment displays and LEDs.

---

## 5. Limitations and Practical Constraints

### 1) Clock Cycle Alignment
Mixing 1-byte and 2-byte instructions required careful handling to keep all pipeline stages synchronous. Restructuring the IF stage resolved timing conflicts.

### 2) Hazard Handling
- **Control Hazard**: None occurred as no branch instructions were used.
- **Structural Hazard**: Avoided by separating instruction (ROM) and data (RAM) memories.
- **Data Hazard**: Initially handled with stalls; later resolved via forwarding to reduce performance penalties.

Forwarding allowed the CPU to run at 2500ps vs. 3100ps with stalls only. Multiple forwarding paths were implemented to optimize runtime.

---

## 6. Results

Initial 3-stage implementation executed within ~2300ps. Expansion to 5 stages slightly increased execution time (~2500ps), as the added MEM stage consumed an additional cycle even when unnecessary. Optimization techniques like forwarding improved timing significantly. However, ideal performance depends on:
- Conditional MEM stage activation.
- Balanced combinational logic across stages.
- Enhanced forwarding for complex dependencies.

---

## 7. Expected Outcomes

The project not only aimed to optimize CPU performance but also deliver an educational tool with:
- Fully modular Verilog code.
- Pipeline stage visibility.
- Hardware-level interactivity.

The entire project is open-sourced on GitHub, with source acknowledgment and redistribution approval from Qi Liu (Tsinghua University). This implementation serves as a reference model for students, educators, and developers exploring pipelined CPU architecture.
