# 5stage_pipeline
starting pointing is liuqdev/8-bit-RISC-CPU-verilog that All rights reserved by the Department of Automation, Tsinghua University
# 🔧 8-bit FSM CPU with 5-Stage Pipelining and Hardware Interaction

This project presents a fully functional **8-bit CPU** originally designed with an **FSM (Finite State Machine)** architecture, which has been transformed into a classic **5-stage pipeline** processor. Our final implementation is fully integrated with an FPGA board, enabling real-time interaction with memory and CPU states via toggle switches and buttons.

## 📌 Project Overview

- **Top-level Module**: `cpu_pipeline.v`
- **Architecture**: FSM-based control redesigned into 5 pipeline stages:
  - **IF**: Instruction Fetch  
  - **ID**: Instruction Decode  
  - **EX**: Execute  
  - **MEM**: Memory Access  
  - **WB**: Write Back

## ✨ Key Features

### 🚀 FSM-to-Pipeline Transformation
Originally an FSM-controlled 8-bit CPU, this project now utilizes a fully decoupled 5-stage pipeline to improve performance by executing multiple instructions in parallel.

### 🧠 8-bit Instruction Set
Compact and expressive instruction set suitable for small-scale embedded processing and architectural education.

### 🧩 Modular Verilog Design
Each pipeline stage is implemented as a separate module to ensure clean design and easy debugging.

### 🎮 Hardware Interaction via FPGA

- **Toggle Switch Input to RAM**  
  Use hardware toggle switches on the FPGA board to input data directly into RAM in real-time, enabling flexible CPU operation without reprogramming ROM.

- **Live State Monitoring with Buttons**  
  With the push of a button, users can read the current values stored in ROM, RAM, and Register File and observe how the pipeline evolves step-by-step.

### 📚 Educational Purpose
This project was built to deepen our understanding of pipelined processor design, hazard resolution, and memory interfacing. By implementing real-time debugging features through board-level interaction, we made the CPU transparent, testable, and highly interactive for learning purposes.

## 🛠 Technologies Used

- **Verilog HDL**
- **ModelSim for Simulation**
- **Intel Quartus / FPGA Development Board**
- **Toggle switches & push buttons for input/output**

## 📸 Demonstration

> *(Insert board image or simulation waveform here)*

---

Made with 🧠 and 💻 by passionate digital designers.
