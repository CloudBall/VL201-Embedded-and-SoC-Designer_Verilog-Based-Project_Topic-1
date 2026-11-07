# FPGA-Based FIFO Reliability Monitoring System

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Verilog HDL](https://img.shields.io/badge/Language-Verilog--2001-blue.svg)](https://en.wikipedia.org/wiki/Verilog)
[![FPGA Target](https://img.shields.io/badge/FPGA-Xilinx%20Artix--7-orange.svg)](https://www.xilinx.com/products/silicon-devices/fpga/artix-7.html)
[![EDA Tool](https://img.shields.io/badge/EDA-Vivado%202023.2%2B-red.svg)](https://www.xilinx.com/products/design-tools/vivado.html)

A high-reliability, synthesizable synchronous FIFO buffer featuring integrated hardware health diagnostics, sticky error monitoring, on-chip JTAG logic analysis (Vivado ILA), and real-time physical telemetry via a multiplexed 4-digit seven-segment display.

Developed under the **VL201: Embedded and SoC Designer** program at the **National Institute of Electronics and Information Technology (NIELIT), Chennai**.

---

## 📌 Architecture Highlights

- **Dynamic Diagnostic Watermarks:** Continuous word-count tracking with dual watermark detection (`Almost_Full` $\ge 14$, `Almost_Empty` $\le 2$).
- **Sticky Error Logging:** Latches illegal read/write transactions (`Underflow` and `Overflow` exceptions) until explicitly serviced or reset.
- **Word-Level Parity Verification:** Real-time even-parity bit evaluation on memory outputs.
- **Dual-Tier Hardware Observability:**
  - **Embedded ChipScope/ILA:** Deep internal trace capability via JTAG for cycle-accurate debugging.
  - **Board-Level 7-Segment Display:** Time-multiplexed (~763 Hz) occupancy counter with alphanumeric error symbols (`O` for overflow, `U` for underflow).
- **Physical Input Conditioning:** Built-in two-stage synchronizer and hysteresis counter debouncing push-button strobes.

---

## 🗂️ Repository Structure

```text
├── constraints/
│   └── Basys3_Artix7.xdc        # Pin mapping & 100 MHz timing constraints
├── docs/
│   └── VL201_Project_Report.md   # Complete project documentation & theory
├── rtl/
│   ├── button_debounce.v        # Pushbutton synchronizer and debouncer
│   ├── fifo_reliable_core.v     # Core FIFO with reliability engines
│   ├── seven_seg_controller.v   # Time-multiplexed 7-segment display driver
│   └── top_fifo_reliability.v   # Top-level FPGA integration wrapper
├── scripts/
│   └── run_vivado.tcl           # Automated TCL script for synthesis & P&R
├── tb/
│   └── tb_fifo_reliable_core.v  # Self-checking verification testbench
└── README.md
```

---

## 📊 FPGA Resource Utilization (Artix-7 XC7A35T)

| Resource Primitive | Total Used | Target Available | Utilization (%) |
| :--- | :--- | :--- | :--- |
| **Slice LUTs** | 128 | 20,800 | 0.61% |
| **Slice Registers (FF)** | 184 | 41,600 | 0.44% |
| **Block RAM Tile** | 2.5 | 50 | 5.00% |
| **Clock Buffers (BUFG)**| 1 | 32 | 3.12% |
| **Bonded IOBs** | 22 | 106 | 20.75% |

**Timing Closure:**
- **Worst Negative Slack (WNS):** $+6.842\text{ ns}$ (at 100 MHz)
- **Maximum Frequency ($F_{max}$):** **~316.6 MHz**

---

## 🚀 Getting Started

### Prerequisites
- AMD/Xilinx Vivado (2020.1 or newer) OR Icarus Verilog (`iverilog`) + GTKWave for behavioral simulation.
- Target Board: Digilent Basys 3 or any Xilinx Artix-7 development board.

### 1. Simulation using Icarus Verilog
```bash
iverilog -o sim_fifo tb/tb_fifo_reliable_core.v rtl/fifo_reliable_core.v
vvp sim_fifo
# Open VCD waveform in GTKWave
gtkwave tb_fifo_reliable_core.vcd
```

### 2. Automated Vivado Build (Batch Mode)
```bash
vivado -mode batch -source scripts/run_vivado.tcl
```

### 3. Hardware Deployment & Pin Mapping
- **`CLK`**: 100 MHz oscillator (`W5`)
- **`RST`**: Push Button Up (`T18`)
- **`WR_EN`**: Push Button Center (`U18`)
- **`RD_EN`**: Push Button Down (`U17`)
- **`SW[7:0]`**: 8-bit input data bus (`V17` to `W13`)
- **`LED[5:0]`**: Status indicators (`Empty`, `Almost_Empty`, `Almost_Full`, `Full`, `Overflow`, `Underflow`)
- **`AN[3:0]` & `SEG[6:0]`**: 4-digit display showing current occupancy count and diagnostic alarms.

---

## 📄 License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
