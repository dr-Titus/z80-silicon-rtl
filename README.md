# Z80 NMOS Silicon-to-RTL CPU Core

**Version:** 0.9.0-alpha  
**Author:** Andrey Titov (dr.Titus)  
**License:** GPL v3  

## Overview
This repository contains a fully synchronous, cycle-accurate RTL model of the original Zilog NMOS Z80 microprocessor, written in Verilog. 

Unlike traditional open-source Z80 cores that rely on a "black-box" approach—emulating instruction behavior based on observable outputs and test results—this core is reconstructed directly from the original silicon topology. It captures the true hardware mechanics of the Z80, translating the original analog and asynchronous transistor-level design into a clean digital abstraction.

## Key Features
- **Silicon-Derived Logic:** The architecture perfectly mirrors the original hardware implementation, including undocumented instructions, complex interrupt handling, and exact bus timings.

- **100% Accurate Undocumented Mechanics:** Cycle-accurate behavior of all undocumented features, including the `SCF`/`CCF` flags (along with bits 3 and 5) and the `MEMPTR` (`WZ`) register mechanics.

- **Accurate "Special Reset":** The model precisely replicates the Z80's unique hardware reset sequence exactly as it is implemented on the original die.

- **Single Clock Operation:** No external multipliers or multi-phase clocks are required. The core runs on a single external `CLK` signal, identical to the real chip.

- **Latch-Free & Fully Synchronous:** The original Z80 design heavily utilized transparent latches, pass transistors, bidirectional internal buses, and dynamic charge storage on transistor gates. This core has been entirely stripped of these analog artifacts and converted into a strict, fully synchronous RTL design, making it highly suitable for FPGA synthesis and software simulation (e.g., via Verilator).


## Interface & Integration Notes
To facilitate integration into modern FPGA environments and simulation wrappers, please note the following interface design choices:
- **Active-High Signals:** All external control pins (e.g., `MREQ`, `IORQ`, `RD`, `WR`) are provided as direct (active-high) signals, rather than inverted (active-low) as they are on the physical chip.
- **Split Data Bus:** The bidirectional data bus is split into separate `data_in` and `data_out` ports. 
- **Tri-State (High-Z) Controls:** To interface with physical bidirectional buses on real hardware, use the `data_z` output to set the data bus to a high-impedance (Z) state. Corresponding `adr_z` and `controls_z` signals are provided to tri-state the address and control buses respectively.


## Source Data & Methodology
The reverse-engineering path was as follows:
1. Used the netlist from the Z80 Explorer project, kindly converted into P-CAD format by **Vslav**.
2. Untangled the 8,500 transistors to create a structured transistor-level schematic.
3. Created an asynchronous logic schematic directly from the transistor schematic.
4. Converted the asynchronous schematic into a fully synchronous logic schematic.

- **100% Manual Work:** No AI or automated logic-extraction tools were used. The logic was untangled entirely by hand.

<img width="1773" height="1489" alt="Z80-Example-Transistor-Gate" src="https://github.com/user-attachments/assets/0e6265ff-1cf1-447f-ac0b-217b39ffb646" />

## Usage & Testing
This repository provides the raw Verilog core files. The core is designed to be a direct drop-in replacement. You can integrate the `.v` files directly into your existing FPGA projects or C++/Verilator simulation wrappers.

### Testing with Verilator & EmuStudio
To validate the core with extensive software test suites (such as `ZEXALL` and `ZEXDOC`), the RTL was compiled using **Verilator**. The resulting C++ model was then integrated into the **EmuStudio** emulator for execution.

The complete pre-configured simulation package, test environment, and project discussion are available on the official forum thread:

**[ZX-PK.ru - EmuStudio v0.9 test 48 (Pentagon RTL)](https://zx-pk.ru/threads/34173-revers-inzhiniring-z80.html?p=1229078&viewfull=1#post1229078)**


## Current Status: 0.9.0-alpha
The core has successfully passed all standard Z80 tests available to me, showing a 100% match with the native Zilog NMOS Z80. 


## Notes
- **Language:** Inline source code comments in files are currently in Russian. English translations will be added in future updates.


## References
- **Hardware mechanics and reverse-engineering analysis:** [zx-pk.ru thread by dr.Titus](https://zx-pk.ru/threads/34173-revers-inzhiniring-z80.html) *(in Russian)*
- **Original Netlist Source:** [Z80 Explorer by Goran Devic](https://github.com/gdevic/Z80Explorer)

