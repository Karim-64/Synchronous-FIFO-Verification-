# Synchronous FIFO Verification

![Status](https://img.shields.io/badge/Status-Completed-success)
![Coverage](https://img.shields.io/badge/Coverage-100%25-brightgreen)
![Language](https://img.shields.io/badge/Language-SystemVerilog-blue)
![Simulator](https://img.shields.io/badge/Simulator-QuestaSim-red)

## 📜 Project Overview
This project focuses on the **functional verification** of a Synchronous FIFO (First-In-First-Out) memory buffer. The verification environment is built using a **Layered SystemVerilog Testbench** architecture (Class-based), ensuring modularity and reusability.

The project involves creating a robust verification plan, implementing a self-checking testbench with a Golden Reference Model (Scoreboard), achieving 100% Functional and Code Coverage, and identifying design bugs using constrained random stimulus.

## 🛠️ Design Specifications
* **Type:** Synchronous FIFO
* [cite_start]**Width:** 16-bit [cite: 6996]
* [cite_start]**Depth:** 8 locations [cite: 6996]
* **Interface Signals:**
    * **Inputs:** `clk`, `rst_n`, `wr_en`, `rd_en`, `data_in`
    * **Outputs:** `data_out`, `full`, `empty`, `almostfull`, `almostempty`, `overflow`, `underflow`, `wr_ack`

## 🏗️ Verification Environment Architecture
The testbench is organized using a layered class-based approach:

| Component | Description |
| :--- | :--- |
| **Transaction** | [cite_start]Defines the payload and randomization constraints (e.g., 70% write distribution, 30% read)[cite: 7006]. |
| **Generator (TB)** | Generates constrained random stimulus and drives the interface. |
| **Monitor** | [cite_start]Samples interface signals and converts them into transaction objects[cite: 7218]. |
| **Scoreboard** | [cite_start]Implements the **Golden Reference Model** using a SystemVerilog `queue` to predict expected outputs and compare them with the DUT[cite: 7077]. |
| **Coverage** | [cite_start]Defines `covergroups` to track functional coverage of control signals and corner cases[cite: 7148]. |
| **Interface** | [cite_start]Connects the DUT to the Testbench using `modports`[cite: 7026]. |

## 🐞 Bugs Found & Fixed
During the verification process, **5 critical logic bugs** were identified in the RTL and fixed:

1.  [cite_start]**Reset Logic Error:** Sequential output flags were not being reset to 0 upon assertion of `rst_n`[cite: 7410].
2.  [cite_start]**Async Underflow:** The `underflow` flag responded asynchronously instead of synchronously when `rd_en` was high on an empty FIFO[cite: 7414].
3.  [cite_start]**Almost Full Logic:** The `almostfull` flag triggered at count 6 instead of the expected count 7 (Depth-1)[cite: 7418].
4.  [cite_start]**Simultaneous RW (Empty):** When writing and reading simultaneously while **Empty**, the `empty` flag remained high incorrectly (Read should be ignored, Write should proceed)[cite: 7422].
5.  [cite_start]**Simultaneous RW (Full):** When writing and reading simultaneously while **Full**, the flags did not update correctly (Write should be ignored, Read should proceed)[cite: 7426].

## 📊 Verification Results

The testbench achieved **100% coverage** across all metrics.

### Coverage Metrics
| Metric | Status | Percentage |
| :--- | :---: | :---: |
| **Functional Coverage** | ✅ | [cite_start]100% [cite: 7354] |
| **Code Coverage (Statement)** | ✅ | [cite_start]100% [cite: 7352] |
| **Code Coverage (Branch)** | ✅ | [cite_start]100% [cite: 7350] |
| **Code Coverage (Toggle)** | ✅ | [cite_start]100% [cite: 7348] |
| **Assertions** | ✅ | [cite_start]100% Passing [cite: 7358] |

### Assertions (SVA)
Immediate and Concurrent assertions were used to verify:
* Reset behavior.
* Flag logic (`full`, `empty`, `overflow`, `underflow`).
* Pointer wrapping and boundary checks.
* Write Acknowledge timing.

## 🚀 How to Run
The project is configured for **Siemens EDA QuestaSim**.

1.  **Compile the Design and Testbench:**
    ```bash
    vlib work
    vlog +define+SIM *.sv +cover -covercells
    ```

2.  **Run Simulation:**
    ```bash
    vsim -voptargs=+acc work.FIFO_top -cover -sv_seed random -l sim.log
    ```

3.  **Execute Macro:**
    ```tcl
    do wave.do
    run -all
    ```

4.  **Generate Coverage Report:**
    ```bash
    coverage save FIFO_monitor.ucdb -onexit
    ```

## 👥 Author
* **Karim Mohamed**
