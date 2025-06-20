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

# Inception of open-source EDA, OpenLANE and Sky130 PDK

## 🧩 Advanced Breakdown: QFN-48 Packaging, Silicon Die Architecture, IP Integration & RISC-V Based ASIC Design

### 📦 QFN-48 Package Architecture

The **QFN-48 (Quad Flat No-Lead, 48-pin)** is a thermally-enhanced, leadframe-based surface-mount package widely adopted for low-profile, high-performance ICs. It provides:
- **48 edge-terminal contact pads** for signal I/O and power delivery.
- **Exposed die attach pad (DAP)** for thermal grounding and low-inductance path to the PCB.
- **Enhanced Electrical Performance**: Reduced parasitics (capacitance and inductance), ideal for RF, high-speed digital, and analog mixed-signal SoCs.

> This package type is suitable for ASICs requiring high pin-count and efficient thermal dissipation within a compact footprint.

![QFN Package](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/arduino%20chip.png)
![Package Detail](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/package.png)

---

### 🧠 Silicon Die Anatomy: Core, Pads, Macros & IPs

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

### 🧮 Instruction Set Architecture: RISC-V

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

### ⚙️ Software-to-Hardware Translation Pipeline

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

### 🏗️ RTL2GDSII: ASIC Physical Design Flow

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
#### 6. Find problem in the DRC section of the old magic tech file for the skywater process and fix them.

Link to Sky130 Periphery rules: [https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html)

Commands to download and view the corrupted skywater process magic tech file and associated files to perform drc corrections

```bash
# Change to home directory
cd

# Command to download the lab files
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

# Since lab file is compressed command to extract it
tar xfz drc_tests.tgz

# Change directory into the lab folder
cd drc_tests

# List all files and directories present in the current directory
ls -al

# Command to view .magicrc file
gvim .magicrc

# Command to open magic tool in better graphics
magic -d XR &
```

Screenshots of commands run

![Screenshot from 2024-03-21 22-33-57](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/1b4cf68e-fa83-4d44-9b08-ca2b63ceb471)
![Screenshot from 2024-03-21 22-34-09](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/1bc14ddb-feb6-4052-bc12-0f018f09c343)

Screenshot of .magicrc file

![Screenshot from 2024-03-21 22-35-58](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/89b46a0f-63b7-445c-bf2b-e6cda16853c7)

**Incorrectly implemented poly.9 simple rule correction**

Screenshot of poly rules

![Screenshot from 2024-03-21 22-54-49](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/9260cf37-5933-44a1-8362-597183644334)

Incorrectly implemented poly.9 rule no drc violation even though spacing < 0.48u

![Screenshot from 2024-03-21 22-54-19](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/acfbcf69-020e-4b62-96bd-b7630aa74ef0)
![Screenshot from 2024-03-21 23-54-11](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a05bd29a-b181-4e26-826a-d32f12696b2c)

New commands inserted in sky130A.tech file to update drc

![Screenshot from 2024-03-21 23-58-44](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/dc30df54-3282-42f0-8e7d-fc4d8877ed64)
![Screenshot from 2024-03-21 23-09-50](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d112ca07-854c-41e7-8822-c269e21defea)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![Screenshot from 2024-03-21 23-13-11](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b18e8e07-ef0f-40fb-9b6d-8aae878a23c6)
![Screenshot from 2024-03-22 00-00-40](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d5afe8d8-691b-485d-a89a-8f901e18b56e)

**Incorrectly implemented difftap.2 simple rule correction**

Screenshot of difftap rules

![Screenshot from 2024-03-22 00-14-47](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/086b7a66-b60a-470a-b5c0-a5ac938ebec3)

Incorrectly implemented difftap.2 rule no drc violation even though spacing < 0.42u

![Screenshot from 2024-03-22 00-14-36](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a2d0d739-2df5-4eb5-ab78-c80d366e24e4)

New commands inserted in sky130A.tech file to update drc

![Screenshot from 2024-03-22 00-26-43](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b5892f9b-9c5d-4b1b-baa2-6fe45f3965b1)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![Screenshot from 2024-03-22 00-29-22](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a3f92160-6701-48fb-b6cf-e4c41dc4a531)

**Incorrectly implemented nwell.4 complex rule correction**

Screenshot of nwell rules

![Screenshot from 2024-03-22 00-51-34](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/4ad4901d-0b9a-4339-89e3-7bb3fce2766d)

Incorrectly implemented nwell.4 rule no drc violation even though no tap present in nwell

![Screenshot from 2024-03-22 00-52-51](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/87da8944-0ad8-455d-97ec-3909eac656c3)

New commands inserted in sky130A.tech file to update drc

![Screenshot from 2024-03-22 01-03-42](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/886c6930-6314-4a6f-97d9-6b8423444ac0)
![Screenshot from 2024-03-22 01-04-04](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d9808e9a-42c2-4421-9b82-2ef65a5a1ad7)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Change drc style to drc full
drc style drc(full)

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![Screenshot from 2024-03-22 01-10-25](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/49b1004d-f860-4ca7-86f4-4d79784a01cf)


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
To run the Static Timing Analysis(STA) in other terminal:<br/>
```
\# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

\# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/sta1.png)
We get a wns and slack violated value of -36.62 and tns value of -3854.15, which is very high.<br/><br/>
Now, running the synthesis for minimized slack violation:<br/>
```
# prep design so as to update variables
prep -design picorv32a -tag 24-03_10-03 -overwrite

# Additional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to display current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to display current value of variable SYNTH_BUFFERING
echo $::env(SYNTH_BUFFERING)

# Command to display current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Command to set reduce MAX_FANOUT to 4
echo $::env(SYNTH_MAX_FANOUT) 4

# Now that the design is prepped and ready, we can run synthesis
run_synthesis
```
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/re_synth.png)
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/syn_results.png)
<br/>Now the tns and wns value is minimized to 0.00
<br/>Again running STA to reduce slack violations by manually reducing delays:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/sta_2.png)
<br/><br/>Here we got the max delay as 0.81 (of '_02560_' net)
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/max_delay_net.png)
<br/><br/>To reduce this delay we have to replace the cell under driver pins of desired net:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/config_delays_sta.png)
<br/><br/>Again running the STA and checking wether the maximum delay and slack is reduced:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/sta_3.png)
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/max_delay_reduced.png)
<br/><br/>As we can clearly see, the delay and slack is reduced by a significant factor.
<br/>To do basic timing ECO: ```report_checks -from _35312_ -to _35239_ -through _32503_```
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/timing_ECO.png)
<br/>Now run floorplan```run_floorplan```, placement```run_placement``` and cts(clock tree synthesis) ```run_cts```.
<br/><br/>Let's check some parameters like clock buffer list, root buffer, capacitance, etc based on the cts.tcl file:
<br/><br/>Screenshot of cts.tcl file:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/cts_tcl.png)
<br/><br/>Screenshot of displaying parameters and invoking openROAD ```openroad```.
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/openroad.png)


# Post-CTS OpenROAD timing analysis
The commands required to succesfully implement OpenROAD:
```
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/31-01_17-10/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/31-01_17-10/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/31-01_17-10/results/synthesis/picorv32a.synthesis_cts.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all clocks as propagated clocks
set_propagated_clock \[all_clocks\]

# Check syntax of \'report_checks\' command
help report_checks

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/openroad_cmds.png)
<br/><br/>This will generate a hold and a setup report:
<br/>Screenshot of Hold report:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/hold_report.png)
<br/><br/>Screenshuot of Setup report
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/setup_report.png)
<br/><br/>Inboth the cases the timing slack is 'MET' and not violated.
<br/>But we can improve the slack even more, even though it has negative effect like increase in area. We can reduce the slack by removing the 'clkbuf_1' from the buffer list.
```
# Checking current value of \'CTS_CLK_BUFFER_LIST\'
echo \$::env(CTS_CLK_BUFFER_LIST)

# Removing \'sky130_fd_sc_hd\_\_clkbuf_1\' from the list
set ::env(CTS_CLK_BUFFER_LIST) \[lreplace \$::env(CTS_CLK_BUFFER_LIST) 0 0\]

# Checking current value of \'CTS_CLK_BUFFER_LIST\'
echo \$::env(CTS_CLK_BUFFER_LIST)

# Checking current value of \'CURRENT_DEF\'
echo \$::env(CURRENT_DEF)

# Setting def as placement def
set ::env(CURRENT_DEF)/openLANE_flow/designs/picorv32a/runs/17-01_14-04/results/placement/picorv32a.placement.def

# Run CTS again
run_cts

# Checking current value of \'CTS_CLK_BUFFER_LIST\'
echo \$::env(CTS_CLK_BUFFER_LIST)
```
<br/><br/>Screenshot of code implementation:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/run_new_cts.png)
```
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/17-01_14-04/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/17-01_14-04/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts1.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/17-01_14-04/results/synthesis/picorv32a.synthesis_cts.v

# Read library for design
read_liberty \$::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Report hold skew
report_clock_skew -hold

# Report setup skew
report_clock_skew -setup

# Exit to OpenLANE flow
exit
```
<br/><br/>Screenshot of code implementation:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/new_openroad.png)

<br/><br/>New setup and hold reports:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/new_hold.png)
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/new_setup.png)

<br/><br/>It can be clearly noted that the setup has improved by 5.9ps and the hold has improved by 738.2ps which is quite significant.

<br/><br/>We can reinsert the clkbuf_1 into the buffer list after the openroad implementation:
```
# Checking current value of \'CTS_CLK_BUFFER_LIST\'
echo \$::env(CTS_CLK_BUFFER_LIST)

# Inserting \'sky130_fd_sc_hd\_\_clkbuf_1\' to first index of list
set ::env(CTS_CLK_BUFFER_LIST) \[linsert \$::env(CTS_CLK_BUFFER_LIST) 0
sky130_fd_sc_hd\_\_clkbuf_1\]

# Checking current value of \'CTS_CLK_BUFFER_LIST\'
echo \$::env(CTS_CLK_BUFFER_LIST)
```
<br/><br/>Screenshot of code implementation:
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/reinsert_clk_buf_1.png)

# Final steps for RTL2GDS using tritonRoute and openSTA
# DAY-5 LAB
Generation of Power Distribution Network(PDN) and exploring the layout in Magic:
```
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# Since we have aliased the long command to \'docker\' we can invoke the OpenLANE flow docker sub-system by just running this command
docker

# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using
the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now we have to prep the design  
prep -design picorv32a -tag 31-01_17-10

# Check current def
echo  $::env(CURRENT_DEF)

# Now that CTS is done we can do power distribution network
gen_pdn
```
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/pdn_docker.png)
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/pdn_info.png)
<br/><br/>After running pdn, if we check CURRENT_DEF: ```echo  $::env(CURRENT_DEF)```, it is now changed from 'picorv32a.cts.def' to '17-pdn.def'<br/><br/>
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/pdn_end_def.png)

Screenshots of power distribution network run

![Screenshot from 2024-03-26 14-22-34](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/dd916806-6688-4c96-b1af-156b2d4acfe6)
![Screenshot from 2024-03-26 14-22-46](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/1f6ade75-93c2-4b76-bc46-77d1d532a84c)

Commands to load PDN def in magic in another terminal

```bash
# Change directory to path containing generated PDN def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/floorplan/

# Command to load the PDN def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```

Screenshots of PDN def

![Screenshot from 2024-03-26 14-30-52](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b13997fd-296c-4213-b4f9-8f66a7375e47)
![Screenshot from 2024-03-26 14-32-24](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/79b5f158-acf4-4065-a0ec-61007ab465d0)
![Screenshot from 2024-03-26 14-34-03](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/bee921ce-03d5-49fb-a9fc-bcc6e3402f8c)

#### 2. Perfrom detailed routing using TritonRoute and explore the routed layout.

Command to perform routing

```tcl
# Check value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Check value of 'ROUTING_STRATEGY'
echo $::env(ROUTING_STRATEGY)

# Command for detailed route using TritonRoute
run_routing
```

Screenshots of routing run

![Screenshot from 2024-03-26 14-48-29](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/f166be26-f49a-4001-abee-ce395857990f)
![Screenshot from 2024-03-26 15-38-39](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c0c8f372-0293-4fdd-a0a3-691f164e7bed)
![Screenshot from 2024-03-26 15-29-38](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/70a99289-06ea-4eb8-b3b0-4147395c6f9c)

Commands to load routed def in magic in another terminal

```bash
# Change directory to path containing routed def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/results/routing/

# Command to load the routed def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of routed def

![Screenshot from 2024-03-26 15-33-12](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/6eade230-eb96-4d7b-b7a9-7a7db9c2c8b7)
![Screenshot from 2024-03-26 15-30-36](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b1900c55-7470-41b2-8b4f-3af871494d99)
![Screenshot from 2024-03-26 15-31-29](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/5faaca5b-fb6e-4abd-946d-6531b35489b8)
![Screenshot from 2024-03-26 15-32-20](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/594ef79b-4755-4934-a087-33fb24996526)

Screenshot of fast route guide present in `openlane/designs/picorv32a/runs/26-03_08-45/tmp/routing` directory

![Screenshot from 2024-03-26 15-41-18](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/1dc38a57-03c9-45c3-acdb-063731a86433)

#### 4. Post-Route OpenSTA timing analysis with the extracted parasitics of the route.

Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD

```tcl
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.def

# Creating an OpenROAD database to work with
write_db pico_route.db

# Loading the created database in OpenROAD
read_db pico_route.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/synthesis/picorv32a.synthesis_preroute.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Read SPEF
read_spef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.spef

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```

Screenshots of commands run and timing report generated

![Screenshot from 2024-03-26 23-16-16](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/72ef3e8d-7ca2-4b60-89ea-de053f9c2902)
![Screenshot from 2024-03-26 23-17-09](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/5cac9ce5-420a-4eaa-b5f4-09286701e550)
![Screenshot from 2024-03-26 23-17-32](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/7d809f14-66b6-4dd6-8161-2ad8371cfaf9)
![Screenshot from 2024-03-26 23-17-56](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/64ccb1d8-74aa-42b0-88d4-a0f9588d2ca2)

<br/><br/>
# Acknowledgements
- Kunal Ghosh, Co-founder, VLSI System Design.<br/>
- Nickson P Jose, Technical Lead, HCLTech.<br/>
- R. Timothy Edwards, Senior Vice President of Analog and Design, Efabless Corporation.<br/>
