# Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD
2-week intensive workshop on Digital VLSI SoC Design and Planning, covering the complete RTL-to-GDSII flow, organized by VLSI System Design (VSD) in collaboration with NASSCOM. The program focused on Advanced Physical Design using OpenLANE and the Sky130 PDK.

# Digital VLSI SoC Design and Planning
![Static Badge](https://img.shields.io/badge/OS-linux-orange)
![Static Badge](https://img.shields.io/badge/EDA%20Tools-OpenLANE--Flow%2C_Yosys%2C_abc%2C_OpenROAD%2C_TritonRoute%2C_OpenSTA%2C_magic%2C_netgen%2C_GUNA-navy)
![Static Badge](https://img.shields.io/badge/languages-verilog%2C_bash%2C_TCL-crimson)
![GitHub last commit](https://img.shields.io/github/last-commit/sriyanshdash/Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD)
![GitHub language count](https://img.shields.io/github/languages/count/sriyanshdash/Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD)
![GitHub top language](https://img.shields.io/github/languages/top/sriyanshdash/Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD)
![GitHub repo size](https://img.shields.io/github/repo-size/sriyanshdash/Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD)
![GitHub code size in bytes](https://img.shields.io/github/languages/code-size/sriyanshdash/Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD)
![GitHub repo file count (file type)](https://img.shields.io/github/directory-file-count/sriyanshdash/Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD)
<!---
Comments
-->
In this session, we will delve into the end-to-end process of designing an Application-Specific Integrated Circuit (ASIC) using the OpenLane flow. Beginning with the Register Transfer Level (RTL) design, we will systematically traverse each critical stage of the ASIC physical design flow—culminating in the generation of the final layout in the form of a GDSII file.

# Curriculum
- Inception of open-source EDA, OpenLANE and Sky130 PDK <br/>
- Good Floorplan vs Bad Floorplan and Introduction to Library Cells <br/>
- Design Library Cell using Magic Layout and ngspice characterization <br/>
- Pre-Layout Timing Analysis and Importance of Good Clock Tree <br/>
- Final Steps for RTL2GDS

# 🧩 Advanced Breakdown: QFN-48 Packaging, Silicon Die Architecture, IP Integration & RISC-V Based ASIC Design

## 📦 QFN-48 Package Architecture

The **QFN-48 (Quad Flat No-Lead, 48-pin)** is a thermally-enhanced, leadframe-based surface-mount package widely adopted for low-profile, high-performance ICs. It provides:
- **48 edge-terminal contact pads** for signal I/O and power delivery.
- **Exposed die attach pad (DAP)** for thermal grounding and low-inductance path to the PCB.
- **Enhanced Electrical Performance**: Reduced parasitics (capacitance and inductance), ideal for RF, high-speed digital, and analog mixed-signal SoCs.

> This package type is suitable for ASICs requiring high pin-count and efficient thermal dissipation within a compact footprint.

![QFN Package](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/arduino%20chip.png)
![Package Detail](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/package.png)

---

## 🧠 Silicon Die Anatomy: Core, Pads, Macros & IPs

### 🔹 **Core**
The **core area** is the functional heart of the ASIC where RTL logic is realized as standard cell instances. It includes:
- **Combinational logic blocks**, pipelined datapaths, FSMs.
- **Standard Cell Library Instances** (from PDK).
- **Custom-designed or hard macros** (e.g., SRAMs, PLLs, SerDes).
- **Clock distribution network**, local routing channels.

### 🔹 **Die**
The **die** is the final fabricated silicon area containing:
- **Core logic area** (placed and routed cells).
- **Padframe** (I/O and power pad ring).
- **ESD protection circuitry**, guard rings, and analog IP periphery.
Fabricated on a silicon wafer, multiple dies are **diced**, **tested**, and **packaged**.

### 🔹 **Pad Ring / Padframe**
Surrounding the core, the **pad ring** includes:
- **Power/Ground Pads** (VDD, VSS).
- **Input/Output Pads** for signal transmission.
- **Analog pads** with special spacing rules (from LVS/DRC decks).
- **Corner cells**, filler cells, and pad seal rings.

![Die Elements](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/core%2C%20pads%2C%20die.png)

### 🔹 **Macros and Foundry IPs**
- **Hard Macros**: Pre-placed, pre-routed blocks (e.g., memory macros, PLLs, IO PHYs).
- **Soft Macros**: Synthesizable IP cores that integrate into digital flow (e.g., RISC-V cores, UART, SPI).
- **Foundry IPs** from SkyWater or similar PDKs include:
  - **Digital Standard Cell Libraries** (AND, OR, INV, DFF, etc.).
  - **Analog/Mixed-signal IPs** (e.g., Bandgap, LDO).
  - **Memory Compilers**: Auto-generate SRAM blocks.
  - **I/O libraries** (high-voltage tolerant, ESD protected).

![IP Macros](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/macros%2C%20ips.png)

---

## 🧮 Instruction Set Architecture: RISC-V

**RISC-V** is a modular, extensible, open-source **ISA (Instruction Set Architecture)**. Unlike proprietary ISAs (e.g., ARM, x86), RISC-V enables silicon designers to:
- Add **custom extensions** (e.g., DSP, Crypto).
- Perform **hardware-software co-design**.
- Optimize for **performance, power, and area (PPA)**.

ISA levels:
- **RV32I/RV64I**: Base integer instruction set.
- **M Extension**: Integer multiplication/division.
- **A Extension**: Atomic instructions.
- **F/D Extensions**: Floating-point.
- **C Extension**: Compressed instructions (16-bit).

![RISC-V ISA](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/RISC-V%20ISA.png)

---

## ⚙️ Software-to-Hardware Translation Pipeline

The path from high-level software to binary-executable hardware involves several abstraction layers:

- **High-Level Language (HLL)**: C/C++, Python.
- **Compiler (e.g., GCC for RISC-V)**: Translates HLL to assembly.
- **Assembler**: Converts assembly to binary opcode.
- **Linker**: Resolves memory locations, creates executable ELF files.
- **Loader/Bootloader**: Loads program into memory.
- **RTL Implementation (HDL)**: Describes architecture in Verilog/VHDL.

At execution, binary instructions interact with:
- **Pipeline stages (IF, ID, EX, MEM, WB)**
- **Control logic**
- **Register files**
- **Data memory / cache**

![SW to HW](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/sw%20to%20hw.png)

---

## 🏗️ RTL2GDSII: ASIC Physical Design Flow

![ASIC Flow](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/asicflow.png)

ASIC design transforms **Register Transfer Level (RTL)** designs into **GDSII**, a foundry-ready layout format. Steps include:

1. **Logic Synthesis**  
   Converts Verilog RTL to gate-level netlist using **Yosys** and **Liberty (.lib)** cell models.

2. **Floorplanning**  
   Macro/block placement, creation of core area, pad ring definition, and power grid planning.

3. **Placement (Global + Detailed)**  
   Optimizes standard cell positions using **OpenROAD** for timing, congestion, and area.

4. **Clock Tree Synthesis (CTS)**  
   Insertion of buffers/inverters to balance clock paths. Targets low **skew** and **latency**.

5. **Routing**  
   - **Global Routing**: Route estimation using congestion maps.
   - **Detailed Routing**: Legal metal/via layout using DRC-compliant grids (Sky130 supports 6 metal layers).

6. **Sign-off Checks**
   - **DRC (Design Rule Check)**: Rule-based physical check using **Magic**.
   - **LVS (Layout vs Schematic)**: Verifies netlist and layout match using **Netgen**.
   - **STA (Static Timing Analysis)**: Conducted using **OpenSTA** to validate hold/setup margins.

---

## 🔑 ASIC Build Requirements

![ASIC Design](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/asic1.png)

### 🔹 RTL (Register-Transfer Level)
- Describes hardware behavior using **sequential and combinational logic**.
- Captures **pipeline stages**, **FSMs**, datapaths.
- Used as input for synthesis tools.

### 🔹 EDA Tools
- **Yosys**: RTL synthesis.
- **OpenROAD**: Floorplanning, placement, CTS, routing.
- **Magic**: Layout generation and DRC.
- **KLayout**: GDS visualization.
- **Netgen**: Schematic/layout equivalence.
- **OpenSTA**: Timing verification.

### 🔹 PDK (Process Design Kit)
Includes:
- **Technology LEF/DEF files**
- **DRC/LVS rules**
- **Device models (.spice/.lib/.pex)**
- **Digital cell libraries** and **I/O libraries**

---

## 🧪 OpenLane & StriVe SoCs

**OpenLane** is a fully open-source RTL-to-GDSII ASIC design flow built using top-tier open tools and compatible with Sky130 PDK. It supports:
- **Macro hardening**
- **Top-level SoC integration**
- **End-to-end reproducibility**

![StriVe](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/striVe.png)

### 🧬 StriVe Chipsets
- Developed using OpenLane
- Based on **RISC-V architecture**
- Focused on **ultra-low-power**, **custom accelerators**, and **scalable IP integration**
- Enable community-driven, open-source SoC tapeouts and silicon prototyping

---

> 🚀 This project provides hands-on exposure to modern VLSI design flows, enabling RTL-to-GDSII implementation of custom RISC-V-based ASICs using an entirely open-source stack.

# 🔧 Open-Source ASIC Design using OpenLANE and Sky130 PDK  
**Project: RTL2GDSII Flow for picorv32a (RISC-V Core)**  
🧪 *A comprehensive VLSI digital backend flow implementation using open-source tools*

---

## 📌 Introduction

This project walks through the full Application-Specific Integrated Circuit (ASIC) digital backend design flow using the **OpenLANE EDA toolchain** and **Sky130 PDK**. The RTL design under consideration is **picorv32a**, a minimalistic and highly configurable RISC-V core.

Through this, we explore:
- RTL synthesis and analysis
- Floorplanning and standard cell placement
- Clock Tree Synthesis (CTS)
- Detailed routing using up to 6 metal layers
- Timing, DRC, LVS sign-off
- GDSII generation for tape-out

---

## 🧰 Technology Stack

| Layer           | Tool/Resource                           |
|-----------------|------------------------------------------|
| Synthesis       | [Yosys](https://github.com/YosysHQ/yosys) |
| Timing          | [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA) |
| Floorplan/Place/Route | [OpenROAD](https://github.com/The-OpenROAD-Project/OpenROAD) |
| Layout + DRC    | [Magic VLSI](http://opencircuitdesign.com/magic/) |
| Layout Viewer   | [KLayout](https://www.klayout.de/) |
| PDK             | [SkyWater Sky130 PDK](https://github.com/google/skywater-pdk) |
| Flow Wrapper    | [OpenLANE](https://github.com/The-OpenROAD-Project/OpenLane) |

---

## 🗂️ Project Directory Structure

```
openlane/
├── designs/
│   └── picorv32a/
├── runs/
│   └── picorv32a/
├── flow.tcl
├── Dockerfile
└── PDK/
```

- `designs/picorv32a/`: RTL, floorplan configs
- `runs/picorv32a/`: Output logs, reports, DEF, GDSII
- `flow.tcl`: TCL script to control flow interactively

---

## 🚀 Flow Execution Steps

### 1️⃣ Launch Docker

```bash
docker
./flow.tcl -interactive
```

### 2️⃣ Load OpenLANE Environment

```tcl
% package require openlane 0.9
```

### 3️⃣ Prepare Design

```tcl
% prep -design picorv32a
```

Creates `runs/picorv32a/<timestamp>` directory with initialized config and setup files.

---

## 🔧 Synthesis (`run_synthesis`)

The RTL is synthesized using **Yosys**, converting Verilog to gate-level netlist using Sky130 standard cells.

**Important Output**:
- `1-yosys_4.stat.rpt`: Gate stats
- Flip-flop count = 1613  
- Total cell count = 14876  

📊 **Flop Ratio**:
```
= 1613 / 14876 ≈ 0.1084 → ~10.84%
```

> Indicates sequential vs combinational complexity of the design.

---

## 🧱 Floorplanning (`run_floorplan`)

Defines die/core dimensions, IO pins, and power planning.

### ⚙️ Key Parameters
- **Core Utilization**: Area used by cells ÷ total core area
- **Aspect Ratio**: Height ÷ Width of the core
- **Pin Offset, Padding, Power Rings**: Configurable

🟩 Good floorplans offer:
- Efficient routing
- Less congestion
- Better timing closure

---

## 🏗️ Placement (`run_placement`)

Places standard cells on the chip respecting netlist connectivity and physical constraints.

🗂️ Outputs:
- `placement.def`
- Congestion maps
- Global & detailed placement metrics

---

## 🔁 Clock Tree Synthesis (`run_cts`)

Performs insertion of clock buffers and routing to ensure balanced clock latency (using OpenROAD's `TritonCTS`).

🎯 Objectives:
- Minimize **clock skew**
- Reduce **hold/setup violations**
- Ensure **synchronous operation** across the design

---

## 🔌 Routing (`run_routing`)

Performs global and detailed routing using Sky130 metal stack (6 layers).

📎 Outputs:
- Final routed `DEF`
- Parasitic SPEF files
- Congestion statistics

---

## 🔍 Verification

### ✅ Static Timing Analysis (STA)

```tcl
% run_sta
```

- Validates timing closure
- Checks for slack violations
- Reports critical paths and max delay

### ✅ Layout vs Schematic (LVS)

```tcl
% run_lvs
```

- Ensures layout matches synthesized netlist

### ✅ Design Rule Checking (DRC)

```tcl
% run_drc
```

- Confirms layout adheres to Sky130 fabrication rules (spacing, enclosure, overlaps)

---

## 📦 GDSII Generation

```tcl
% run_magic
% run_klayout
```

Generates:
- `final.gds`: Layout file for tape-out
- `final.lef`: Library exchange format for macro integration
- `final.def`: Routing and placement definitions

👀 Use **KLayout** to inspect final layout.

---

## 🧠 About Sky130 PDK

The **Sky130 Process Design Kit** is an open-source 130nm CMOS technology node, developed by **SkyWater** and **Google**.

🧩 Components:
- DRC/LVS rules
- Standard Cell Libraries (`sky130_fd_sc_hd`, `sky130_fd_io`)
- Device Models (BSIM4)
- I/O pads, filler cells, decap cells

---

## 📋 Final Summary Table

| Step        | Command             | Output Files                      |
|-------------|---------------------|-----------------------------------|
| Prep        | `prep -design`      | `runs/picorv32a/` initialized dir |
| Synthesis   | `run_synthesis`     | Netlist, Yosys stats              |
| Floorplan   | `run_floorplan`     | DEF, core layout                  |
| Placement   | `run_placement`     | Cell DEF, congestion map          |
| CTS         | `run_cts`           | Clock buffers, netlist            |
| Routing     | `run_routing`       | Routed DEF, parasitics            |
| DRC/LVS/STA | `run_drc`, `run_lvs`, `run_sta` | Verification logs        |
| GDS Export  | `run_magic`         | `final.gds`, `final.lef`          |

---

## 📘 References

- 🔗 [OpenLANE](https://github.com/The-OpenROAD-Project/OpenLane)
- 🔗 [Sky130 PDK](https://github.com/google/skywater-pdk)
- 🔗 [Yosys](https://github.com/YosysHQ/yosys)
- 🔗 [Magic VLSI](http://opencircuitdesign.com/magic/)
- 🔗 [OpenROAD](https://github.com/The-OpenROAD-Project/OpenROAD)
- 🔗 [KLayout](https://www.klayout.de/)

---
# OpenLANE Floorplanning & Placement – SoC Design Workflow

## Overview

This documentation outlines **Day-2** of the ASIC physical design flow using **OpenLANE**, focusing on the **Floorplanning** and **Placement** stages for a sample RISC-V core (`picorv32a`). Visualizations are performed using **Magic VLSI** along with the **Sky130** open-source PDK.

---

## 🔧 Prerequisites

- OpenLANE installed and configured with Sky130A PDK
- Magic VLSI layout tool installed
- Synthesized RTL netlist ready
- Basic familiarity with digital backend design

---

## 🗺️ Floorplanning with OpenLANE

Floorplanning defines the die/core dimensions, IO locations, cell rows, and power planning. It sets the foundation for all downstream physical design steps.

### ▶️ Run Floorplan:

```
run_floorplan
```

![Running Floorplan](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/fp_run.png)

The `results/floorplan/` directory contains the output `.def` file, which encodes key geometric and placement information such as die area, core rows, pin locations, and macro outlines.

![DEF Floorplan Output](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/fp_diearea.png)

---

### 🧩 View Floorplan in Magic:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef \
def read picorv32a.floorplan.def &
```

![Magic Floorplan View](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/magic_fp.png)

#### 🛠 Magic Tool Shortcuts:

> **Center Design:**
> - `S` → Select all  
> - `V` → Center view  
>
> **Zoom to Region:**
> - Drag with left-click  
> - Right-click and press `Z`  
>
> **Inspect Cell:**
> - Hover & press `S`  
> - In `tkcon`, type `what`  

![tkcon What Command](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/tkcon_fp.png)

---

## 🧱 Placement using OpenLANE

Placement assigns physical locations to standard cells within the core while optimizing timing, area, and routing congestion.

It consists of:
- **Global Placement**: Coarse cell distribution (minimize wirelength)
- **Detailed Placement**: Fine-tunes positions for legal, DRC-clean layout

---

### ▶️ Run Placement:

```
run_placement
```

![Running Placement](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/plcmt_run.png)

![Placement Wirelength](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/wiring_plcmt.png)
![Detailed Placement](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/detailed_plcmt.png)

---

### 🧩 View Placement in Magic:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef \
def read picorv32a.placement.def &
```

![Placement in Magic](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/magic_plcmt.png)
![Zoomed Placement](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/zoomed_plcmt.png)

---

## 📚 Standard Cell Characterization Flow

Each standard cell (e.g., inverter, buffer) goes through electrical characterization to generate timing, power, and noise models used in Static Timing Analysis (STA).

### 🔗 Inputs:

- PDKs (Sky130A)
- DRC/LVS rules
- SPICE transistor models
- Design specs
- Library LEF and LIB files

### 🔁 Steps:

1. Load device models (PMOS/NMOS)
2. Parse extracted SPICE netlist
3. Recognize subcircuits (e.g., inverter)
4. Setup power supplies
5. Apply test stimuli
6. Sweep output load conditions
7. Run simulations using `.tran`, `.dc`, etc.
8. Feed configuration into **GUNA** for LIB generation

### 📤 Outputs:

- `GDSII` (Layout)
- `LEF` (Abstract)
- `.LIB` (Timing and power)
- `CDL` (Netlist)
- SPICE extracted netlist

---

## ⏱️ Timing Characterization in VLSI

Timing analysis models how quickly signals propagate through a gate, ensuring clock and data meet setup/hold constraints.

### Key Parameters:

| Parameter | Description |
|----------|-------------|
| **Propagation Delay** | Time from 50% input to 50% output |
| **Slew Rate** | Time from 20% to 80% (rise/fall transitions) |
| **Thresholds** | Defined voltage % levels for delay/slew (e.g., 20%, 50%, 80%) |
| **Input/Output Rise/Fall** | Points used to evaluate rising/falling transitions |

![Timing Slew Diagram](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/timing_slew.png)


# Design library cell using Magic Layout and ngspice characterization

## DAY-3 LAB

This lab introduces the design, layout, and characterization of a custom CMOS inverter standard cell using open-source tools like Magic and ngspice. The goal is to understand the flow from layout design to simulation-based timing analysis.

### Step 1: Clone Repository and Open Layout

Clone the standard cell design repo:
```bash
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag &
```

![Terminal Commands](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/inv_git_clone.png)

### Step 2: Layout in Magic

![Layout in Magic](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/magic_inv.png)

### Step 3: SPICE Extraction and Simulation

Extract SPICE netlist with parasitic components:
```bash
# In tkcon window
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

![SPICE Extraction](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/tkcon_extract.png)

Modify the SPICE model file as per the `.lib` specifications.

![Modified SPICE](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/inv_spice.png)

Run simulation:
```bash
ngspice sky130_inv.spice
```

![SPICE Simulation](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/spice_run.png)

Plot waveform:
```bash
plot y vs time a
```

![Waveform](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/y_vs_a_plot.png)
![Timing Characteristics](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/time_n_delay.png)

### Step 4: Timing Parameter Extraction

- **Rise Time**: 0.06427 ns
- **Fall Time**: 0.01529 ns
- **Propagation Delay**: 0.06165 ns
- **Cell Fall Delay**: 0.028 ns

## Fixing DRC Errors in Layout

Download and unpack DRC testing files:
```bash
cd
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
vi .magicrc
magic -d XR
```

Open layout and fix DRC errors by modifying the `.tech` file:
- Add `allpolynonres`
- Fix spacing issues and untapped nwell violations
- Re-run DRC:
```bash
tech load sky130A.tech
drc check
drc why
```


## Pre-layout Timing and Delay Table Introduction

- Delay tables model gate and interconnect delays.
- Used during static timing analysis (STA).
- Delay representations include `.lib`, SPEF, RSPF.

## DAY-4 LAB

Set grid for standard cell routing:
```bash
grid 0.46um 0.34um 0.23um 0.17um
```

Verify port alignment and dimensions, then save:
```bash
save sky130_vsdinv.mag
```

Generate and copy LEF file:
```bash
lef write
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```

Update `config.tcl`:
```tcl
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

Run synthesis:
```bash
prep -design picorv32a -tag 31-01_17-10 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```

Update strategy and sizing:
```tcl
set ::env(SYNTH_STRATEGY) 1
set ::env(SYNTH_SIZING) 1
```

Run floorplan:
```bash
run_floorplan
# If error occurs:
init_floorplan
place_io
tap_decap_or
```

Run placement and open layout in Magic:
```bash
run_placement
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/31-01_17-10/results/placement/
magic -T /path/to/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

## Post-Synthesis Timing Analysis with OpenSTA

Configure files:
- `pre_sta.conf` in OpenLANE directory
- `my_base.sdc` in `picorv32a/src`

Extract capacitance from `.lib` and run OpenSTA.

