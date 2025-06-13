# 8-bit FSM CPU with 5-Stage Pipelining and Hardware Interaction

### Authors
- Hwapyung Kim
- Junsoo Moon
- Wonho Lee

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

![image](https://github.com/user-attachments/assets/c4c83993-4a5d-4389-9c58-9fe820c12e0c)

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

![image](https://github.com/user-attachments/assets/d6de593b-2805-473d-99f4-76b5fced3ea4)

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

---

## Additional Technical Implementation Details

This project began with a simple FSM-based single-cycle CPU model and was progressively transformed into a multi-stage pipelined architecture, eventually incorporating real-time system-level interfacing.

### Step 1: 3-Stage Pipeline Design

The initia![image](https://github.com/user-attachments/assets/17f7169c-3d79-4faa-980b-e737875eae51)
l FSM-based codebase was modular, with separate modules like `alu.v`, `controller.v`, `accum.v`, `addr_mux.v`, and `counter.v`. These were restructured into pipelined stages:

![image](https://github.com/user-attachments/assets/b9b96e78-f7ab-4550-aee6-7f61866c1f27)

- **IF Stage (Instruction Fetch)**: Integrated `rom`, `counter`, and `addr_mux` functionalities. The PC was implemented as an 8-bit register, with stall and halt conditions controlling PC advancement and instruction validity. Immediate value handling for 2-byte instructions was also included.

  ![image](https://github.com/user-attachments/assets/387ce23e-5191-4ce2-ad69-21ee064f7c7a)

- **ID Stage (Instruction Decode)**: Combined controller logic and register file access (`reg_32`). Decoded instructions based on opcodes and generated control signals. Data was passed through `IF_ID_reg` pipeline registers to maintain synchronization.


![image](https://github.com/user-attachments/assets/6bdb81bf-6cd3-448d-8a7d-cf22f37bb4d9)
![image](https://github.com/user-attachments/assets/538e24d4-8372-450b-b9e0-42c54eefce96)

  
- **EX Stage (Execute)**: Combined `accum` and `alu` logic. Arithmetic and control operations were performed based on decoded signals. Forwarding and synchronization were handled within this stage, and data/control were passed via `ID_EX_reg`.

### Step 2: Expanding to 5-Stage Pipeline

To mimic a real processor architecture more accurately, MEM (Memory Access) and WB (Write Back) stages were separated:

![image](https://github.com/user-attachments/assets/0b98fa68-e5cb-4a02-a0dd-34d50b18450f)

- **MEM Stage**: Handled ROM/RAM read operations based on opcode. Conditional assignments (e.g., LDA) determined whether `in_mem_data` was routed to the register or accumulator. Controlled using `MEM_WB_reg`.

  ![image](https://github.com/user-attachments/assets/df50765a-f814-44ec-aa86-0fb5163ab7c2)

- **WB Stage**: Managed data writes to either RAM or register file. Write paths were separated using `write`, `dest`, and `data` wires. Final data was written back to the memory blocks instantiated in the top module.

Data dependencies and timing alignment were preserved by introducing intermediate pipeline registers (`EX_MEM_reg`, `MEM_WB_reg`) to ensure stable signal propagation at every clock cycle.

### System-Level Integration

Once the pipeline was complete:
- The CPU was deployed on a Cyclone V FPGA board.
- Users could input data via toggle switches and control memory address selection.
- Data could be observed through 7-segment displays and LEDs.
- RAM and Register contents were directly manipulatable.
- ROM instructions could be observed and stepped through using hardware buttons.

### Forwarding & Hazard Mitigation

![image](https://github.com/user-attachments/assets/00e88b14-d2bb-477d-a3f6-a54157dfaa50)
![image](https://github.com/user-attachments/assets/05a46450-9d04-4191-b8d2-3ff977fd7251)
![image](https://github.com/user-attachments/assets/6eb8820a-74c2-4247-a797-91113563cb03)


Forwarding logic was introduced for instructions like `PRE` → `ADD` and `LDM` → `STO`, allowing the pipeline to bypass unnecessary stalls. Dedicated registers and conditional logic handled data forwarding, significantly improving performance compared to the initial stall-only approach.

---

This detailed code-based restructuring allowed the pipelined CPU to operate with clarity, stability, and observable performance improvements across stages.
