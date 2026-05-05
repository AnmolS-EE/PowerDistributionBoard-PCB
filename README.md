# PowerDistributionBoard-PCB
A high-efficiency power distribution board (PDB) designed to convert a single DC input (24V/10A) into multiple regulated rails for laboratory and personal device charging. This project has a custom-designed buck converter stage, individual USB Power Delivery (PD) controllers, and an ESP32 for real-time telemetry and monitoring. It will be designed on a 4-layer PCB board designed in KiCad.
## Schematic
V1.0 of the schematic is complete. As seen by the image below, it is neatly organized into the four main compartments: MCU(Microcontroller), Power management, Power analysis and USB ports.
![V1.0 Schematic](https://github.com/AnmolS-EE/PowerDistributionBoard-PCB/blob/main/Images/PowerDistributionBoard.pdf)
## PCB Design
The PCB design and layout is currently a work in progress. Schematic and simulations are being thoroughly verified first. 

## Simulations
### 24V to 5V Step-Down Buck Converter
**Objective:** Step down the 24V main system power to a highly stable 5V rail.

**Simulation Note (XL4016 vs. LT1074):** 
The XL4016 used in the KiCad schematic doesn’t have a public SPICE model, so I used the Analog Devices LT1074 to simulate the circuit and verify the LC filter design and transient behavior.
 
 Note: The feedback resistors in the simulation (1kΩ / 1.26kΩ) were chosen to match the LT1074’s 2.21V reference. On the actual PCB, I’m using (1kΩ / 3kΩ) based on the XL4016’s 1.25V reference.

**Simulation & Stress Test:**
I tested the regulator under a continuous 2.5A load (modeled as 2Ω) to check how the 47µH inductor and 470µF output capacitors perform. The capacitors were modeled with a realistic ESR of 50mΩ.

**1. Transient Startup & Output Ripple:**
![5V Rail Startup](https://github.com/AnmolS-EE/PowerDistributionBoard-PCB/blob/main/Simulations/Plots/Buck_24Vto5V_Startup.png)
![5V Rail Ripple](https://github.com/AnmolS-EE/PowerDistributionBoard-PCB/blob/main/Simulations/Plots/Buck_24Vto5V_Ripple.png)

The system ramps up to 5V in less than 2ms. Steady-state analysis at full load shows a peak-to-peak voltage ripple of roughly 40mV (under 1%), confirming the power delivery is quite clean.


### 3.3V Logic Rail
**Objective:** Provide a stable power rail for the ESP32-S3-WROOM-1 microcontroller and I2C peripherals using the AMS1117-3.3. 

**Simulation & Stress Test:**
To verify the stability of the AMS1117-3.3V LDO regulator, the circuit was simulated in LTspice. I applied a 5-ohm load to force a continuous 660mA draw which is double the worst-case scenario in real life.

![LTspice Simulation: 3.3V Rail Schematic and Stress Test Graph](https://github.com/AnmolS-EE/PowerDistributionBoard-PCB/blob/main/Simulations/Plots/AMS1117_Stress_Test_3V3.png)

**Conclusion:**
As shown in the transient analysis above, the input of 5V (blue line) is converted into 3.3V (green line). The total load of the 3.3V rail (red line) remains perfectly flat under a 660mA continuous load with no voltage drop. The schematic confirms the test parameters, proving the power delivery is efficient enough to power the microcontroller and other perpherals.
