# SRAM Architecture Comparison: Standard 6T vs. Abbasi 10T (LP10T)

This repository contains LTspice simulation files and methodologies for comparing a standard 6T SRAM cell against the Abbasi 10T (LP10T) SRAM cell. The analysis focuses on extracting key performance metrics: Static Noise Margin (SNM), Write Delay, and Standby Leakage Power.

## Table of Contents
- [Overview](#overview)
- [File Structure](#file-structure)
- [Simulation Methodology](#simulation-methodology)
- [Results](#results)
- [How to Run Simulations](#how-to-run-simulations)
- [License](#license)
- [Acknowledgments](#acknowledgments)

## Overview
This project investigates the Abbasi 10T SRAM cell's purported advantages over the conventional 6T design, specifically targeting improved SNM and reduced leakage power at low supply voltages (0.7V). The simulations were conducted using LTspice with PTM 45nm transistor models.

**Author**: Utkarsh ([@QubitCrafter](https://github.com/QubitCrafter))

## File Structure
```
SRAM/
├── README.md
├── LICENSE
├── 6T_SRAM_Tests/
│   ├── 45nm.txt          # PTM 45nm transistor model
│   ├── SNM_Test.asc              # Static Noise Margin simulation schematic
│   ├── Leakage_Test.asc          # Standby leakage power simulation schematic
│   └── Write_Delay_Test.asc      # Write delay simulation schematic
└── 10T_SRAM_Tests/
    ├── 45nm.txt          # PTM 45nm transistor model
    ├── Draft1.asc        # Initial 10T draft (for reference)
    ├── SNM_Test.asc              # Static Noise Margin simulation schematic
    ├── Leakage_Test.asc          # Standby leakage power simulation schematic
    └── Write_Delay_Test.asc      # Write delay simulation schematic
```
*Note: The essential .asc schematics and .txt model files are included to reproduce all results. Waveform images are provided in the `assets/` directory for quick visual reference. Large raw simulation output files (.log, .raw, .plt) have been excluded via `.gitignore` for repository clarity.*

## Simulation Methodology
All simulations followed the exact procedures detailed in the original documentation:

### 1. Read Static Noise Margin (SNM) & Read Disturb (SNM_Test.asc)
- **Objective**: Measure maximum DC noise tolerance during read operations
- **Method**: DC sweep analysis with mirrored V(Q) vs V(QB) plots to form butterfly curve
- **Extraction**: Graphical SNM calculation using largest inscribed square in butterfly lobes
- **Results**: 
  - 6T SNM: 100 mV
  - 10T SNM: 190 mV (90 mV improvement)

  <p align="center">
    <img src="assets/6T/SNM.png?v=2" width="45%" alt="6T SNM Waveform">
    <img src="assets/10T/SNM.png?v=2" width="45%" alt="10T SNM Waveform">
    <br><em>Left: 6T SNM | Right: 10T SNM</em>
  </p>

### 2. Standby Leakage Current and Power (Leakage_Test.asc)
- **Objective**: Measure idle power consumption (hold state)
- **Method**: Transient analysis (10ns) with WL/WWLA held at 0V, measuring VDD source current
- **Extraction**: Leakage Power = VDD × I_leakage (steady-state value after initial spike)
- **Results**:
  - 6T Leakage Power: 33,068 pW (47.24 nA)
  - 10T Leakage Power: 345 pW (492.85 pA) - **99% reduction**

  <p align="center">
    <img src="assets/6T/Leakage.png?v=2" width="45%" alt="6T Leakage Waveform">
    <img src="assets/10T/Leakage.png?v=2" width="45%" alt="10T Leakage Waveform">
    <br><em>Left: 6T Leakage Current | Right: 10T Leakage Current</em>
  </p>

### 3. Write Delay (Write_Delay_Test.asc)
- **Objective**: Time required to flip storage node during Write '0' operation
- **Method**: Transient analysis with pulsed WL/WWLA and WBL=0V, initialized with V(Q)=0.7V
- **Extraction**: Write Delay = t(VQ=50%VDD) - t(WL=50%VDD)
- **Results**:
  - 6T Write Delay: 5.95 ps
  - 10T Write Delay: 5.24 ps (12% improvement)

  <p align="center">
    <img src="assets/6T/Write_Delay.png?v=2" width="45%" alt="6T Write Delay Waveform">
    <img src="assets/10T/Write_Delay.png?v=2" width="45%" alt="10T Write Delay Waveform">
    <br><em>Left: 6T Write Delay | Right: 10T Write Delay</em>
  </p>

### Critical Note: 3-Terminal NMOS Trap
The Abbasi 10T design requires careful attention to transistor wiring in LTspice:
- **Trap**: Default 3-terminal NMOS symbols internally connect Body to Source
- **Risk**: Wiring 0.7V to Source instead of Drain forward-biases Body-Drain diode
- **Solution**: Always route power/signals to Drain terminal, or use 4-terminal NMOS with Body tied to GND

## How to Run Simulations
1. Install [LTspice XVII](https://www.analog.com/en/design-center/design-tools-and-calculators/ltspice-simulator.html)
2. Clone this repository
3. Navigate to either test directory (`6T_SRAM_Tests` or `10T_SRAM_Tests`)
4. Open the desired `.asc` file:
   - `SNM_Test.asc` for Static Noise Margin simulation
   - `Leakage_Test.asc` for leakage power simulation
   - `Write_Delay_Test.asc` for write delay simulation
5. Click the "Run" button in LTspice

## Results Summary
| Metric          | Standard 6T SRAM | Abbasi 10T SRAM | Improvement |
|-----------------|------------------|-----------------|-------------|
| SNM             | 100 mV           | 190 mV          | +90 mV      |
| Leakage Power   | 33,068 pW        | 345 pW          | -99%        |
| Write Delay     | 5.95 ps          | 5.24 ps         | -12%        |

The Abbasi 10T SRAM demonstrates significant improvements in noise immunity and leakage power with minimal impact on write performance, validating its suitability for low-power applications.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments
- **Transistor Models**: Predictive Technology Model (PTM) 45nm High Performance Metal Gate / High-K / Strained-Si models from the [University of Minnesota](https://mec.umn.edu/ptm)
- **Abbasi 10T SRAM Architecture**: Based on the Abbasi 10T (LP10T) SRAM cell design.
- **Methodology**: The simulation procedures and measurement techniques align with established SRAM analysis practices.

**Important**: The 45nm.txt files are essential for reproducing the simulations and are included in both test directories as they contain the PTM transistor models required for accurate results.