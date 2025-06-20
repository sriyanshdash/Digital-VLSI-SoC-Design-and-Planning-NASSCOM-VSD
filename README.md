# Digital VLSI SoC Design and Planning
![Static Badge](https://img.shields.io/badge/OS-linux-orange)
![Static Badge](https://img.shields.io/badge/EDA%20Tools-OpenLANE--Flow%2C_Yosys%2C_abc%2C_OpenROAD%2C_TritonRoute%2C_OpenSTA%2C_magic%2C_netgen%2C_GUNA-navy)
![Static Badge](https://img.shields.io/badge/languages-verilog%2C_bash%2C_TCL-crimson)
![GitHub last commit](https://img.shields.io/github/last-commit/sriyanshdash/Digital-VLSI-SoC-Design-and-Planning-NASSCOM-VSD)
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

## Advanced Breakdown: QFN-48 Packaging, Silicon Die Architecture, IP Integration & RISC-V Based ASIC Design

### QFN-48 Package Architecture

The **QFN-48 (Quad Flat No-Lead, 48-pin)** is a thermally-enhanced, leadframe-based surface-mount package widely adopted for low-profile, high-performance ICs. It provides:
- **48 edge-terminal contact pads** for signal I/O and power delivery.
- **Exposed die attach pad (DAP)** for thermal grounding and low-inductance path to the PCB.
- **Enhanced Electrical Performance**: Reduced parasitics (capacitance and inductance), ideal for RF, high-speed digital, and analog mixed-signal SoCs.

> This package type is suitable for ASICs requiring high pin-count and efficient thermal dissipation within a compact footprint.

![QFN Package](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/arduino%20chip.png)
![Package Detail](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/package.png)

---

### Silicon Die Anatomy: Core, Pads, Macros & IPs

### **Core**
The **core area** is the functional heart of the ASIC where RTL logic is realized as standard cell instances. It includes:
- **Combinational logic blocks**, pipelined datapaths, FSMs.
- **Standard Cell Library Instances** (from PDK).
- **Custom-designed or hard macros** (e.g., SRAMs, PLLs, SerDes).
- **Clock distribution network**, local routing channels.

### **Die**
The **die** is the final fabricated silicon area containing:
- **Core logic area** (placed and routed cells).
- **Padframe** (I/O and power pad ring).
- **ESD protection circuitry**, guard rings, and analog IP periphery.
Fabricated on a silicon wafer, multiple dies are **diced**, **tested**, and **packaged**.

### **Pad Ring / Padframe**
Surrounding the core, the **pad ring** includes:
- **Power/Ground Pads** (VDD, VSS).
- **Input/Output Pads** for signal transmission.
- **Analog pads** with special spacing rules (from LVS/DRC decks).
- **Corner cells**, filler cells, and pad seal rings.

![Die Elements](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/core%2C%20pads%2C%20die.png)

### **Macros and Foundry IPs**
- **Hard Macros**: Pre-placed, pre-routed blocks (e.g., memory macros, PLLs, IO PHYs).
- **Soft Macros**: Synthesizable IP cores that integrate into digital flow (e.g., RISC-V cores, UART, SPI).
- **Foundry IPs** from SkyWater or similar PDKs include:
  - **Digital Standard Cell Libraries** (AND, OR, INV, DFF, etc.).
  - **Analog/Mixed-signal IPs** (e.g., Bandgap, LDO).
  - **Memory Compilers**: Auto-generate SRAM blocks.
  - **I/O libraries** (high-voltage tolerant, ESD protected).

![IP Macros](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/macros%2C%20ips.png)

---

### Instruction Set Architecture: RISC-V

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

### Software-to-Hardware Translation Pipeline

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

### RTL2GDSII: ASIC Physical Design Flow

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

## ASIC Build Requirements

![ASIC Design](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/asic1.png)

### RTL (Register-Transfer Level)
- Describes hardware behavior using **sequential and combinational logic**.
- Captures **pipeline stages**, **FSMs**, datapaths.
- Used as input for synthesis tools.

### EDA Tools
- **Yosys**: RTL synthesis.
- **OpenROAD**: Floorplanning, placement, CTS, routing.
- **Magic**: Layout generation and DRC.
- **KLayout**: GDS visualization.
- **Netgen**: Schematic/layout equivalence.
- **OpenSTA**: Timing verification.

### PDK (Process Design Kit)
Includes:
- **Technology LEF/DEF files**
- **DRC/LVS rules**
- **Device models (.spice/.lib/.pex)**
- **Digital cell libraries** and **I/O libraries**

---

## OpenLane & StriVe SoCs

**OpenLane** is a fully open-source RTL-to-GDSII ASIC design flow built using top-tier open tools and compatible with Sky130 PDK. It supports:
- **Macro hardening**
- **Top-level SoC integration**
- **End-to-end reproducibility**

![StriVe](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/striVe.png)

### StriVe Chipsets
- Developed using OpenLane
- Based on **RISC-V architecture**
- Focused on **ultra-low-power**, **custom accelerators**, and **scalable IP integration**
- Enable community-driven, open-source SoC tapeouts and silicon prototyping

---
## Introduction

This project walks through the full Application-Specific Integrated Circuit (ASIC) digital backend design flow using the **OpenLANE EDA toolchain** and **Sky130 PDK**. The RTL design under consideration is **picorv32a**, a minimalistic and highly configurable RISC-V core.

Through this, we explore:
- RTL synthesis and analysis
- Floorplanning and standard cell placement
- Clock Tree Synthesis (CTS)
- Detailed routing using up to 6 metal layers
- Timing, DRC, LVS sign-off
- GDSII generation for tape-out

---

## Technology Stack

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

## Project Directory Structure

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

## Flow Execution Steps

### Launch Docker

```bash
docker
./flow.tcl -interactive
```
![flow tcl -interactive](https://github.com/user-attachments/assets/18d2ce1b-142d-4e23-a3bd-5bf9ab860d77)

### Load OpenLANE Environment

```tcl
% package require openlane 0.9
```
![flow tcl -interactive](https://github.com/user-attachments/assets/58e9a24a-d619-408d-87d5-514cee64e05b)

### Prepare Design

```tcl
% prep -design picorv32a
```
![prep -design file](https://github.com/user-attachments/assets/8bdfe015-0e2f-416b-8724-4368fdd9e1a9)

Creates `runs/picorv32a/<timestamp>` directory with initialized config and setup files.
![runs file](https://github.com/user-attachments/assets/b230cfbb-50f7-4fd1-a77a-6d4587d05885)

---

## Synthesis (`run_synthesis`)

The RTL is synthesized using **Yosys**, converting Verilog to gate-level netlist using Sky130 standard cells.

**Important Output**:
- `1-yosys_4.stat.rpt`: Gate stats
- Flip-flop count = 1613  
- Total cell count = 14876  
![no of d ffs](https://github.com/user-attachments/assets/12e0d158-ba37-474f-88c8-338413070fea)

 **Flop Ratio**:
```
= 1613 / 14876 ≈ 0.1084 → ~10.84%
```
![count of dffs](https://github.com/user-attachments/assets/8d7129ac-eb46-414d-bc95-16b46c95b275)

> Indicates sequential vs combinational complexity of the design.

---

## Floorplanning (`run_floorplan`)

Defines die/core dimensions, IO pins, and power planning.

### Key Parameters
- **Core Utilization**: Area used by cells ÷ total core area
- **Aspect Ratio**: Height ÷ Width of the core
- **Pin Offset, Padding, Power Rings**: Configurable

 Good floorplans offer:
- Efficient routing
- Less congestion
- Better timing closure

---

## Placement (`run_placement`)

Places standard cells on the chip respecting netlist connectivity and physical constraints.

 Outputs:
- `placement.def`
- Congestion maps
- Global & detailed placement metrics

---

## Clock Tree Synthesis (`run_cts`)

Performs insertion of clock buffers and routing to ensure balanced clock latency (using OpenROAD's `TritonCTS`).

 Objectives:
- Minimize **clock skew**
- Reduce **hold/setup violations**
- Ensure **synchronous operation** across the design

---

## Routing (`run_routing`)

Performs global and detailed routing using Sky130 metal stack (6 layers).

📎 Outputs:
- Final routed `DEF`
- Parasitic SPEF files
- Congestion statistics

---

## Verification

### Static Timing Analysis (STA)

```tcl
% run_sta
```

- Validates timing closure
- Checks for slack violations
- Reports critical paths and max delay

### Layout vs Schematic (LVS)

```tcl
% run_lvs
```

- Ensures layout matches synthesized netlist

### Design Rule Checking (DRC)

```tcl
% run_drc
```

- Confirms layout adheres to Sky130 fabrication rules (spacing, enclosure, overlaps)

---

## GDSII Generation

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

## About Sky130 PDK

The **Sky130 Process Design Kit** is an open-source 130nm CMOS technology node, developed by **SkyWater** and **Google**.

Components:
- DRC/LVS rules
- Standard Cell Libraries (`sky130_fd_sc_hd`, `sky130_fd_io`)
- Device Models (BSIM4)
- I/O pads, filler cells, decap cells

---

## Final Summary Table

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

## References

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

## Floorplanning with OpenLANE

Floorplanning defines the die/core dimensions, IO locations, cell rows, and power planning. It sets the foundation for all downstream physical design steps.

### Good floorplan vs bad floorplan and introduction to library cells
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/w_h_core.png)
Utilization Factor and Aspect Ratio:<br/>
To determine the Utilization Factor and Aspect Ratio, it is essential to first define the height and width of both the core and die areas.<br/>
The core area refers to the region within a chip that accommodates all the logic cells and circuit components. This is where the primary logic operations take place.<br/>
The die area, on the other hand, encompasses the core and serves as the space for placing I/O components and connections.<br/>
The dimensions of the core area are determined by the netlist of the design, which specifies the number of components required to implement the logic. Consequently, the height and width of the die area are dictated by the dimensions of the core area.<br/><br/>
For instance, consider a netlist consisting of two logic gates and two flip-flops, each occupying an area of 1 square unit. Since the netlist includes a total of four elements, the minimum core area required to accommodate these components would be 4 square units.<br/>
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/eg_netlist.png) ![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/w_h_netlist.png)
Utilization Factor: <br/>
The Utilization Factor is the ratio of the area occupied by the netlist to the total available core area. For an optimized FloorPlan, the Utilization Factor should be less than 1. If it reaches 1, there won’t be any extra space for adding additional logic, making the FloorPlan 100% utilized. <br/>
Utilization Factor = Area occupied by netlist / Total core area <br/>

Aspect Ratio: <br/>
The Aspect Ratio represents the proportion between the height and width of the core. A core with an aspect ratio of 1 forms a square, whereas any other value results in a rectangular shape.<br/>
Aspect Ratio = Height of the core / Width of the core<br/>
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/min_area_netlist.png)

In this case,<br/>
Utilization Factor = (4 x 1 sq. unit)/(2 unit x 2 unit) = 1<br/>
Hence, the core area is 100% utilized by the netlist.<br/>
Aspect Rtio = (2 unit)/(2 unit) = 1<br/>
Hence, the core has a square shape.<br/>
​
<br/>Now, after adding pre-placed cells, decoupling capacitors, power planning and pin placement the corresponding floorplan will look like:<br/>
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/fp_concepts.png)

### Run Floorplan:

```
run_floorplan
```

![floorplan command run](https://github.com/user-attachments/assets/8a3c36f1-060c-44ed-a0b3-19aad8641ec7)


The `results/floorplan/` directory contains the output `.def` file, which encodes key geometric and placement information such as die area, core rows, pin locations, and macro outlines.

![floorplan def file](https://github.com/user-attachments/assets/df5a9312-b012-4e8d-bb17-013344f8dae6)


---

### View Floorplan in Magic:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef \
def read picorv32a.floorplan.def &
```
![floorplan in magic 1](https://github.com/user-attachments/assets/dd3d84c1-766a-4f9b-914d-bead24aeb695)

![floorplan in magic 2](https://github.com/user-attachments/assets/26fa08c9-b370-494e-8d61-32b2319dba57)

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

![floorplan in magic 3](https://github.com/user-attachments/assets/42977a48-64c2-4ae5-98ab-32049074cf39)

---

## Placement using OpenLANE

Placement in VLSI (Very Large Scale Integration) is a critical step in the physical design flow, where standard cells, macros, and IP blocks are assigned precise physical locations within the chip’s core area. This stage directly follows logic synthesis and precedes routing, with the primary objective of optimizing the design for performance, power, and area (PPA).

The placement process is typically divided into two main phases: **global placement** and **detailed placement**. During global placement, cells are distributed across the chip to minimize total wirelength and reduce congestion, often allowing for temporary overlaps and relaxed alignment constraints to achieve a rough but optimized arrangement. Analytical, partitioning-based, and stochastic optimization algorithms are commonly employed in this phase to efficiently handle large-scale designs. Following global placement, detailed placement refines the cell positions to eliminate overlaps, ensure alignment with site rows, and further optimize for timing and routability. This phase leverages incremental and local optimization techniques to fine-tune the layout and address any remaining design rule violation.

After establishing the floorplan with the desired utilization factor and aspect ratio, placement begins by mapping the synthesized netlist onto the chip’s core. Special attention is paid to the placement of IO pins and the minimization of interconnect lengths, as longer wires can introduce significant timing delays. To mitigate these delays and maintain signal integrity, buffers are strategically inserted in critical paths where the distance between standard cells or between a standard cell and an IO pin exceeds predefined thresholds. This approach ensures that timing constraints are met and that the overall design remains robust and manufacturable.

![chrome_iXJtmHj6Tp](https://github.com/user-attachments/assets/0ac0c555-2eff-4d70-a126-86656c8617bf)

![chrome_R2xVsZ8ZFy](https://github.com/user-attachments/assets/e7607eb0-334b-4c13-b825-a57ed6283d33)

---

### Run Placement:

```
run_placement
```

![placement command run](https://github.com/user-attachments/assets/d3d7aed5-9279-4754-9557-d300e934a555)

---

### View Placement in Magic:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef \
def read picorv32a.placement.def &
```

![placement in magic](https://github.com/user-attachments/assets/0cca44b3-3891-4a2c-ad63-9020de4ac201)
![zoomed placement](https://github.com/user-attachments/assets/764040b9-b30b-466d-a539-57706eb806e3)

---

## What are Standard Cells and What is the Standard Cell Characterization Flow

Standard cells are pre-designed, pre-characterized, and pre-verified logic blocks that form the fundamental building blocks of digital integrated circuits, particularly in ASIC and VLSI design. Each standard cell encapsulates a specific logic function—such as AND, OR, NOT gates, or more complex functions like flip-flops and multiplexers—and is designed to a fixed height and footprint for easy integration and automated placement and routing. This modular approach allows designers to focus on higher-level circuit design while leveraging reliable, well-tested components.

The standard cell characterization flow in VLSI involves analyzing and modeling the electrical behavior of these cells so that their performance can be accurately represented in design tools. The process typically includes designing the cell at the transistor level, creating its layout, extracting parasitics to model real-world effects, and then simulating the cell under various conditions to measure timing, power, and noise characteristics. The results are compiled into a Liberty (.lib) file, which is used by synthesis, placement, and routing tools to ensure accurate timing closure and power estimation.

GUNA is a commercial characterization software tool used in the VLSI industry to automate the extraction of timing, noise, and power models from standard cells. By feeding GUNA the transistor-level netlist, technology models, and simulation stimuli, it runs simulations across different process, voltage, and temperature (PVT) corners. GUNA then processes the results to generate the required Liberty (.lib) files, providing EDA tools with the necessary data for accurate digital implementation and verification. This automation streamlines the characterization process, making it efficient and scalable for large standard cell libraries.
Each standard cell (e.g., inverter, buffer) goes through electrical characterization to generate timing, power, and noise models used in Static Timing Analysis (STA).

### Inputs:

- PDKs (Sky130A)
- DRC/LVS rules
- SPICE transistor models
- Design specs
- Library LEF and LIB files

### Steps:

1. Load device models (PMOS/NMOS)
2. Parse extracted SPICE netlist
3. Recognize subcircuits (e.g., inverter)
4. Setup power supplies
5. Apply test stimuli
6. Sweep output load conditions
7. Run simulations using `.tran`, `.dc`, etc.
8. Feed configuration into **GUNA** for LIB generation

### Outputs:

- `GDSII` (Layout)
- `LEF` (Abstract)
- `.LIB` (Timing and power)
- `CDL` (Netlist)
- SPICE extracted netlist
  
![Cell Design Flow](https://github.com/user-attachments/assets/bf655cc2-795e-46c6-9e45-608758800aca)
![GUNA software](https://github.com/user-attachments/assets/d3bf02c0-8ec3-473c-81c3-94c993b3306f)

---

## Timing Characterization in VLSI

Timing characterization is a fundamental process in VLSI design that involves measuring and modeling the temporal behavior of digital circuits, particularly standard cells. By analyzing how signals propagate through logic gates under various conditions, timing characterization enables accurate prediction of circuit performance, ensures timing closure, and helps avoid violations that could lead to chip failure. This process is essential for generating timing models, such as Liberty (.lib) files, which are used by synthesis, placement, and routing tools to verify that the design meets all timing constraints.

During timing characterization, circuits are simulated across a range of process, voltage, and temperature (PVT) conditions to capture variations in timing behavior. The results quantify how quickly signals transition between logic states and how long it takes for a change at the input to affect the output. This data is critical for static timing analysis (STA), which checks that all signal paths in the design meet the required timing specifications.

### Key Parameters:

| Parameter | Description |
|----------|-------------|
| **Timing Threshold** | These are predefined voltage levels, typically expressed as a percentage of the supply voltage (VDD), used to define when a signal is considered to have transitioned between logic states. |
| **Propagation Delay** | The time for a signal change at a gate’s input to reach its output, measured between matching voltage thresholds (usually 50% of input to 50% of output) |
| **Slew Rate (Transition Time)** | The slew rate, or transition time, refers to the time it takes for a signal to transition between two specified voltage thresholds, such as from 20% to 80% of VDD. This parameter indicates how quickly a signal rises or falls |
| **Slew Low Rise Threshold (slew_low_rise_thr)** | This is the lower voltage threshold used to start measuring the rise transition time of a signal, typically set at 20% of VDD |
| **Slew High Rise Threshold (slew_high_rise_thr)** | This is the upper voltage threshold used to end the measurement of the rise transition time, typically set at 80% of VDD |
| **Slew Low Fall Threshold (slew_low_fall_thr)** | This is the lower voltage threshold used to start measuring the fall transition time of a signal, typically set at 20% of VDD |
| **Slew High Fall Threshold (slew_high_fall_thr)** | This is the upper voltage threshold used to end the measurement of the fall transition time, typically set at 80% of VDD |
| **Input Rise Threshold (in_rise_thr)** | This is the voltage threshold used to determine when a rising transition occurs at the input of a gate |
| **Input Low Threshold (in_low_thr)** | This is the voltage threshold used to determine when a falling transition occurs at the input of a gate |
| **Output Rise Threshold (out_rise_thr)** | This is the voltage threshold used to determine when a rising transition occurs at the output of a gate |
| **Output Low Threshold (out_low_thr)** | This is the voltage threshold used to determine when a falling transition occurs at the output of a gate |

![Timing Parameters](https://github.com/user-attachments/assets/60b4788b-4f85-4c90-bfac-88ee08026ec0)

# Design library cell using Magic Layout and ngspice characterization

## DAY-3 LAB

This lab introduces the design, layout, and characterization of a custom CMOS inverter standard cell using open-source tools like Magic and ngspice. The goal is to understand the flow from layout design to simulation-based timing analysis.

### Clone Repository and Open Layout

Clone the standard cell design repo:
```bash
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag &
```
Screenshot of above commands in terminal.
![vsdstccell clone command](https://github.com/user-attachments/assets/677ae83b-b408-4bf7-b6f8-541659828a84)
<br/>Layout of custom CMOS inverter in Magic.
![inverter in magic](https://github.com/user-attachments/assets/9758a608-fc70-4343-a969-857d818b43ad)

### SPICE Extraction and Simulation

Extract SPICE netlist with parasitic components:
```bash
# In tkcon window
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

![ext2spice](https://github.com/user-attachments/assets/c2472429-ab6b-4700-aa25-85145104900e)


Modify the SPICE model file as per the `.lib` specifications.

![modified spice file](https://github.com/user-attachments/assets/7becc1ed-1e1e-4f25-9890-e9a8b8a1b5ae)


Run simulation:
```bash
ngspice sky130_inv.spice
```

![running of ngspice](https://github.com/user-attachments/assets/8ee247da-f8af-4f9e-b9dd-78eea12b28e9)


Plot waveform:
```bash
plot y vs time a
```

![inverter in ngpsice](https://github.com/user-attachments/assets/b1e1e1a8-905f-4625-b3e8-bda5a2960de2)

![Timing Characteristics](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/time_n_delay.png)

<br/><br/>From this plot we can calculate various parameters related to timings and delays of the CMOS inverter characteristics.<br/><br/>
Characterizing the parameters:<br/><br/>
Rise Time: The time taken for the output waveform to transition from 20% to 80% of its maximum value.<br/>
Using data points:<br/>
x0 = 2.18257e-09, y0 = 0.66<br/>
x1 = 2.24684e-09, y1 = 2.64<br/>
Rise time = x1 - x0 = 0.06427 ns<br/><br/>
Fall Time: The time taken for the output waveform to transition from 80% to 20% of its maximum value.<br/>
Using data points:<br/>
x0 = 4.07995e-09, y0 = 2.64<br/>
x1 = 4.09524e-09, y1 = 0.66<br/>
Fall time = x1 - x0 = 0.01529 ns<br/><br/>
Propagation Delay: The time taken for a 50% transition at the output(low to high) corresponding to a 50% transition at the input(high to low).<br/>
Using data points:<br/>
x0 = 2.21153e-09, y0 = 1.65018<br/>
x1 = 2.14988e-09, y1 = 1.65018<br/>
Propagation delay = x1 - x0 = 0.06165 ns<br/><br/>
Cell Fall Delay: The time taken for a 50% transition at the output(high to low) corresponding to a 50% transition at the input(low to high).<br/>
Using data points:<br/>
x0 = 4.078e-09, y0 = 1.65007<br/>
x1 = 4.05e-09, y1 = 1.65007<br/>
Cell fall delay = x1 - x0 = 0.028 ns<br/>


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

![Screenshot from 2025-06-17 19-37-09](https://github.com/user-attachments/assets/b48f44b9-3db7-4a01-935b-9508480cbe1f)


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


## Pre-layout Timing Analysis and Importance of a good Clock Tree
Introduction to Delay Tables:<br/>
Delay tables in VLSI are structured data sets within timing libraries (such as Liberty files) that specify the delay of logic cells as a function of one or more variables, most commonly input transition time (slew) and output load capacitance. These tables are typically generated through extensive circuit simulation (such as SPICE) across a range of realistic operating conditions, capturing the nonlinear relationship between input signal transitions, output loading, and gate delay.

<br/>Purpose of Delay Tables:<br/>
The primary purpose of delay tables is to provide accurate and efficient delay calculations for each cell in the design database during the physical design flow. Instead of performing complex transistor-level simulations for every cell during synthesis, placement, or routing, EDA tools use these pre-characterized delay tables to quickly estimate timing for millions of instances. This approach is essential for enabling practical static timing analysis (STA), where the tool sums up delays along timing paths using values interpolated from the tables.

<br/>During physical design, delay tables serve multiple functions:<br/>
The importance of delay tables in physical design cannot be overstated. They allow designers to achieve timing closure by ensuring that timing predictions are both accurate and fast, supporting optimization for performance, power, and area. By modeling the real-world behavior of cells under varying load and slew conditions, delay tables enable robust analysis across process, voltage, and temperature corners, which is critical for reliable chip manufacturing and operation.

<br/>Types of Delay Representation:<br/>
- Library Characterization Data (Lib Files) – These include lookup tables (LUTs) that provide delays based on input slew and output capacitance.
- Parasitic Delay Models (SPEF, RSPF) – Account for wire delays and coupling effects.
- Propagation Delay Tables – Define delays for individual gates under different operating conditions

## DAY-4 LAB

Commands to open the custom inverter layout.<br/>
Change directory to vsdstdcelldesign: ```cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign```
<br/>Command to open custom inverter layout in magic: ```magic -T sky130A.tech sky130_inv.mag &```
<br/>Screenshot of tracks.info of sky130_fd_sc_hd:<br/><br/>
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/tracks_info.png)

Set grid for standard cell routing:
```bash
grid 0.46um 0.34um 0.23um 0.17um
```
![Screenshot from 2025-06-18 21-18-56](https://github.com/user-attachments/assets/52c34787-629e-4481-b899-033d841b9f87)

<br/>Once the three conditions are verified, i.e.,
<br/>Condition 1: The input and output ports of the standard cell should lie on the intersection of the vertical and horizontal tracks.
<br/>Condition 2: Width of the standard cell should be odd multiples of the horizontal track pitch.
<br/>Condition 3: Height of the standard cell should be even multiples of the vertical track pitch.
Verify port alignment and dimensions, then save:
```bash
save sky130_vsdinv.mag
```
![Screenshot from 2025-06-19 00-29-03 - 1](https://github.com/user-attachments/assets/985decb0-9bbc-4e8d-a2fa-8eb378190803)

Generate and copy LEF file:
```bash
lef write
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```
![Screenshot from 2025-06-19 00-30-02](https://github.com/user-attachments/assets/09955dfc-e352-4091-9c95-36aaa6322324)
![Screenshot from 2025-06-19 00-32-22](https://github.com/user-attachments/assets/cb8ed453-4ae0-45f3-b936-ea158b5933cd)

Update `config.tcl`:
```tcl
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```
![Screenshot from 2025-06-19 00-40-15](https://github.com/user-attachments/assets/a550573d-500a-42e5-a4d4-a98243682c36)

Run synthesis:
```bash
prep -design picorv32a -tag 31-01_17-10 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```
![Screenshot from 2025-06-19 00-46-14](https://github.com/user-attachments/assets/b76d08b8-33df-4c81-b39a-b183fca1552d)
![synthesis command run](https://github.com/user-attachments/assets/3fca224a-dd1c-46d9-b256-b0b529975c30)


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
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/19-06_08-08/results/placement/
magic -T /path/to/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```
![placement command run](https://github.com/user-attachments/assets/44c8452a-ab3f-4215-9e15-444c348fcc08)
![Screenshot from 2025-06-19 01-38-29](https://github.com/user-attachments/assets/ccae1489-2884-4d9f-85b4-79d2c46927b0)
![Screenshot from 2025-06-19 01-43-08](https://github.com/user-attachments/assets/4dec58d4-9839-4c94-b791-fbd60d8c5c9a)
![Screenshot from 2025-06-19 01-43-23](https://github.com/user-attachments/assets/5d057b6f-152d-477e-8ecc-4e617d56e7a4)
![Screenshot from 2025-06-19 01-44-23](https://github.com/user-attachments/assets/d6fcbb3d-b1fd-485f-bea4-ab5d9ea828d4)
![Screenshot from 2025-06-19 01-45-21](https://github.com/user-attachments/assets/6773cdde-1ae1-4802-970e-7a1c6d5e2e8e)

## Post-Synthesis Timing Analysis with OpenSTA

Configure files:
- `pre_sta.conf` in OpenLANE directory
- `my_base.sdc` in `picorv32a/src`
![Screenshot from 2025-06-19 03-35-24](https://github.com/user-attachments/assets/8dcc1b75-ec2d-4459-b223-97f8b22428a8)
![Screenshot from 2025-06-19 03-48-33](https://github.com/user-attachments/assets/92f52e93-e44a-4505-9c82-c55288ce42f1)

Extract capacitance from `.lib` and run OpenSTA.
![Screenshot from 2025-06-19 12-19-22](https://github.com/user-attachments/assets/e6ee04cd-aef4-46e3-bd6c-6a06493e4fb2)
To run the Static Timing Analysis(STA) in other terminal:<br/>
```
\# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

\# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```

![Screenshot from 2025-06-19 03-50-17](https://github.com/user-attachments/assets/bcd648fb-64d4-45f7-b3b3-d649daefa2bf)
We get a wns and slack violated value of -0.33 and tns value of -0.72, here we can see that the slack is violated.<br/><br/>
Now, running the synthesis for minimized slack violation:<br/>
```
# prep design so as to update variables
prep -design picorv32a -tag 19-06_08-08 -overwrite

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
![Screenshot from 2025-06-19 12-22-31](https://github.com/user-attachments/assets/85fccc1b-648f-48c4-8f6a-8a7065430e9c)
![Screenshot from 2025-06-19 12-44-19](https://github.com/user-attachments/assets/8458e0d1-78b2-4941-8b0b-062b854034e0)
![Screenshot from 2025-06-19 12-50-06](https://github.com/user-attachments/assets/6191accf-0d72-42b6-bde9-72a97433abd8)

<br/>Now the tns and wns value is minimized to 0.00
<br/>Again running STA to reduce slack violations by manually reducing delays:
![Screenshot from 2025-06-19 12-49-24](https://github.com/user-attachments/assets/9aae3c64-17c6-41a3-a0b5-7c629798a254)
<br/><br/>Here we got the max delay as 0.23 (of '_16171_' net)
![Screenshot from 2025-06-19 12-58-21](https://github.com/user-attachments/assets/f1dc4295-9791-4ead-8c76-ab29f1c557db)
<br/><br/>To reduce this delay we have to replace the cell under driver pins of desired net:
![Screenshot from 2025-06-19 13-01-18](https://github.com/user-attachments/assets/90614d1e-d225-4741-9084-e556cfe5eaca)
<br/><br/>Again running the STA and checking wether the maximum delay and slack is reduced:
![Screenshot from 2025-06-19 13-11-12](https://github.com/user-attachments/assets/ffdfc9b1-bd62-45cd-ab0b-2e09c19031cc)
<br/><br/>As we can clearly see, the delay and slack is reduced by a significant factor.
<br/>To do basic timing ECO: ```report_checks -from _32396_ -to _32288_```
![Screenshot from 2025-06-19 13-01-18](https://github.com/user-attachments/assets/94267703-d874-487e-b5c5-a8215cebe0c6)
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
read_lef /openLANE_flow/designs/picorv32a/runs/19-06_08-08/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/19-06_08-08/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/19-06_08-08/results/synthesis/picorv32a.synthesis_cts.v

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
![Screenshot from 2025-06-19 18-49-10](https://github.com/user-attachments/assets/2350842e-9cdb-4198-81e6-2cb2ad3a0331)

<br/><br/>This will generate a hold and a setup report:
<br/>Screenshot of Hold report:
![Screenshot from 2025-06-19 18-49-26](https://github.com/user-attachments/assets/a2d69bcd-8405-46c7-9703-da1c28d9db9e)
<br/><br/>Screenshuot of Setup report
![Screenshot from 2025-06-19 18-49-30](https://github.com/user-attachments/assets/fa8ba9af-d347-49f9-811b-4db6ff1893d3)
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
set ::env(CURRENT_DEF)/openLANE_flow/designs/picorv32a/runs/19-06_08-08/results/placement/picorv32a.placement.def

# Run CTS again
run_cts

# Checking current value of \'CTS_CLK_BUFFER_LIST\'
echo \$::env(CTS_CLK_BUFFER_LIST)
```
```
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/19-06_08-08/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/19-06_08-08/results/cts/picorv32a.cts.def

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
prep -design picorv32a -tag 19-06_08-08

# Check current def
echo  $::env(CURRENT_DEF)

# Now that CTS is done we can do power distribution network
gen_pdn
```
![Screenshot from 2025-06-19 18-13-44](https://github.com/user-attachments/assets/e1eadb72-35fc-44e6-94ae-5807aaf933ac)
![Screenshot from 2025-06-19 18-13-48](https://github.com/user-attachments/assets/bb438636-b17c-4ecf-9d27-c15a75b4c6dd)

<br/><br/>After running pdn, if we check CURRENT_DEF: ```echo  $::env(CURRENT_DEF)```, it is now changed from 'picorv32a.cts.def' to '17-pdn.def'<br/><br/>
![](https://github.com/Samsh-Tabrej/nasscom-vsd-soc-design/blob/main/media/pdn_end_def.png)

Commands to load PDN def in magic in another terminal

```bash
# Change directory to path containing generated PDN def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/19-06_08-08/tmp/floorplan/

# Command to load the PDN def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```

Screenshots of PDN def
![Screenshot from 2025-06-19 18-19-51](https://github.com/user-attachments/assets/eb7626fc-0b5b-408c-8cb4-ddf61fd10e8e)
![Screenshot from 2025-06-19 18-20-24](https://github.com/user-attachments/assets/ce64bb8c-df3a-41f8-ad8f-894e6309a4ec)
![Screenshot from 2025-06-19 18-20-33](https://github.com/user-attachments/assets/0b9b94db-fde0-46f0-85e6-df38497de47f)

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
![Screenshot from 2025-06-19 18-25-26](https://github.com/user-attachments/assets/e37015e1-375d-4029-a8c8-9d8b8156e80d)
![Screenshot from 2025-06-19 18-25-31](https://github.com/user-attachments/assets/32d75a2d-0b63-4385-b8dc-f4450fe40009)
![Screenshot from 2025-06-19 18-25-46](https://github.com/user-attachments/assets/6a055576-b584-453c-b0a6-574433fd3577)

Commands to load routed def in magic in another terminal

```bash
# Change directory to path containing routed def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/19-06_08-08/results/routing/

# Command to load the routed def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of routed def

![Screenshot from 2025-06-19 19-29-29](https://github.com/user-attachments/assets/9391d902-282f-4c4b-afc0-1177450a31fd)
![Screenshot from 2025-06-19 19-29-59](https://github.com/user-attachments/assets/bb46729a-d5ef-40c9-8b8d-b8c824e340ed)
![Screenshot from 2025-06-19 19-30-05](https://github.com/user-attachments/assets/c0cd0700-1913-4f27-a3bc-1e15922b966e)

Screenshot of fast route guide present in `openlane/designs/picorv32a/runs/19-06_08-08/tmp/routing` directory

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
![Screenshot from 2025-06-19 18-44-51](https://github.com/user-attachments/assets/3acac10f-9f4c-430f-916e-0e0c1bb5037c)
![Screenshot from 2025-06-19 18-49-10](https://github.com/user-attachments/assets/c716d74e-325c-4782-b03f-a27505fb39a7)
![Screenshot from 2025-06-19 18-49-22](https://github.com/user-attachments/assets/9f7d8f0a-afde-440c-8cd6-18196030dc13)
![Screenshot from 2025-06-19 18-49-24](https://github.com/user-attachments/assets/932265c3-69bb-48f2-9615-b7c45a1e1a9c)
![Screenshot from 2025-06-19 18-49-26](https://github.com/user-attachments/assets/f6566696-13a5-4abc-b5d7-7d2452ca4286)
![Screenshot from 2025-06-19 18-49-28](https://github.com/user-attachments/assets/db5666e6-918d-4cd8-bcde-208ecf39a355)
![Screenshot from 2025-06-19 18-49-29](https://github.com/user-attachments/assets/e531a044-f8c1-45ca-aa1f-fe1e422921b9)
![Screenshot from 2025-06-19 18-49-30](https://github.com/user-attachments/assets/f17b428e-0fb5-493d-8422-9b9d9c526e0c)

<br/><br/>
# Acknowledgements
- Kunal Ghosh, Co-founder, VLSI System Design.<br/>
- Nickson P Jose, Technical Lead, HCLTech.<br/>
- R. Timothy Edwards, Senior Vice President of Analog and Design, Efabless Corporation.<br/>
