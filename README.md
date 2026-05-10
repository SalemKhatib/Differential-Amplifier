# 3-Stage NMOS Differential Amplifier Design & Simulation

[![Tools](https://img.shields.io/badge/Tools-PSPICE%20%7C%20OrCAD-blue.svg)](#)
[![Domain](https://img.shields.io/badge/Domain-Analog%20IC%20Design-orange.svg)](#)

## Overview
This repository contains the complete design, mathematical analysis, and PSPICE simulation of a 3-stage, fully differential NMOS amplifier. The project demonstrates a rigorous analog design methodology: establishing strict theoretical bias points and small-signal parameters via hand-calculation, followed by schematic capture and AC/DC simulation verification. 

The architecture is designed to meet specific gain, output resistance, and steep frequency roll-off characteristics, relevant for precision mixed-signal applications and sensor interfacing.

## Design Specifications: Target vs. Achieved

| Parameter | Target Specification | Simulated Result | Error Margin |
| :--- | :--- | :--- | :--- |
| **Total Gain ($A_v$)** | 36.0 dB | 36.17 dB | ~0.4% |
| **Lower Cutoff ($f_L$)** | 9 kHz | 9.22 kHz | ~2.4% |
| **Upper Cutoff ($f_H$)** | 900 kHz | 879 kHz | ~2.3% |
| **Low-Freq Slope ($M_1$)**| +20 dB/dec | +20 dB/dec | Confirmed |
| **High-Freq Slope ($M_2$)**| -40 dB/dec | -40 dB/dec | Confirmed |
| **Output Resistance ($R_{out}$)**| 5 kΩ | 5 kΩ | Confirmed |

## Architecture & Topology
The circuit utilizes a **Double-Input, Double-Output Differential** topology across all three stages, utilizing exclusively NMOS transistors ($V_{th} = 0.5V, k = 1mA/V^2, \lambda = 0$).

* **First & Third Stages:** Act as the input interface and output driver respectively, ensuring proper impedance matching and driving capability.
* **Second Stage:** Serves as the primary intermediate gain stage.
* **Biasing Strategy:** Three independent current mirrors ($M_{9,10}$, $M_{7,8}$, $M_{3,4}$) are implemented to act as ideal tail current sources. Symmetrical design ensures uniform drain current ($I_D$) splitting across the differential pairs, heavily stabilizing the DC operating points against variations.

### Frequency Response Engineering
A critical component of this design was placing poles and zeros to achieve the targeted $+20$ dB/dec and $-40$ dB/dec roll-offs:
* **Low Frequency (+20 dB/dec):** Coupling capacitors ($C_2, C_3$) placed between the 2nd and 3rd stages block DC bias propagation, providing a zero at the origin and a dominant pole defining $f_L$.
* **High Frequency (-40 dB/dec):** Achieved via two dominant poles. The first is created by $C_6$ and $C_7$ at the 3rd stage input. The second is introduced by differential capacitor $C_4$ bridging the final outputs ($V_{out1}, V_{out2}$), doubling the roll-off rate beyond $f_H$.

## Small-Signal Mathematical Analysis
Rigorous hand-calculations were performed prior to simulation to ensure first-pass success. The total theoretical gain was calculated as $64.5 V/V$ ($36.2$ dB), deviating from the 36.0 dB target by only 2.2%.

$$A_p = A_1 \cdot A_2 \cdot A_3 = g_{m12}(R_2 \parallel R_3) \cdot g_{m5}(R_{13} \parallel R_{12} \parallel R_{11}) \cdot g_{m1}R_5$$

* **Stage 1 Gain:** $A_1 = 1.74$ V/V
* **Stage 2 Gain:** $A_2 = 12.36$ V/V
* **Stage 3 Gain:** $A_3 = 3.0$ V/V

*(Detailed derivations for $g_m$, bias point KVLs, and saturation validations can be found in the `docs/Project_Report.pdf`)*

## Repository Structure
* `/docs` - Contains the comprehensive PDF report detailing all KVL equations, transconductance calculations, and pole/zero derivations.
* `/sim` - Contains the raw PSPICE source files (`.net`, `.lib`, `.sim`, `.cir`). *Note: Temporary and proprietary binary files have been excluded for cleanliness.*

## Getting Started
To view the simulations:
1. Ensure you have Cadence OrCAD / PSPICE 17.2+ installed.
2. Open the `test_1.sim` profile.
3. Run the AC Sweep to generate the Bode plots verifying the 36 dB peak and target cutoff frequencies.
