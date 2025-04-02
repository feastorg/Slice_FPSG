# Docs

Main page for the documentation of the project.

## Schematic Design Notes

Notes on design.

### External Clock

- **Oscillator:** ACO-8.000MHZ-EK
  - 8 MHz XO (Standard) HCMOS, TTL Oscillator, 5V enable/disable
  - Package: 14-DIP, 4 Leads (Metal Can)
  - Product: (See Digikey)
  - Procurement: [Digikey](https://www.digikey.ca/short/9n0brwjd)
  - Datasheet: [Abracon ACO Datasheet](https://abracon.com/Oscillators/ACO.pdf)
- **Connection:**

  - Powered by a dedicated, low-noise 5V rail (from a TPS7A4901 set to 5V)
  - Feed clock output to:
    - ADC’s CLKIN/XTAL1 pin (XTAL2 left unconnected or per datasheet recommendation)
    - Nano Every’s external clock input (D2/PA00 on the ATmega4809)
  - Ensures ADC’s modulator clock and MCU SPI SCLK are synchronous
  - Reference: [Nano Every External Oscillator Info - Arduino Subreddit](https://www.reddit.com/r/arduino/comments/vyi9wo/using_an_external_oscillator_with_the_nano_every/)

    - The onboard programmer on the Nano Every is fully independent and does not rely on bootloaders or clock speed restrictions like the Atmega328-based Arduinos.
    - You don’t need to set fuses via the Arduino IDE; instead, the clock source can be configured at runtime.
    - To change the clock source to external (PA00, Arduino pin D2), use the following code snippet:

      ```
      CPU_CCP = 0xD8;           // Enable writing to protected config registers for the next 4 cycles
      CLKCTRL.MCLKCTRLA = 0x03; // Set clock output off, select external clock source (PA00)
      ```

    - This proposed method allows you to switch to an external oscillator without needing a fuse reprogramming tool.

### Instrumentation Amplifier

- **Amplifier:** AD8426
  - Dual instrumentation amplifier (2 channels per package; use 4 for 8 channels)
  - Product: [AD8426](https://www.analog.com/en/products/ad8426.html)
  - Procurement: [Digikey](https://www.digikey.ca/short/vj3493t8)
  - Datasheet: [AD8426 Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/AD8426.pdf)
- **REF Pin:**
  - Maintain low source impedance (<2 Ω recommended)
  - Use a buffered reference (OP1177, see below)

### Analog Reference Voltage Sources

- **Main Regulator (Analog Front End):** TPS7A4901DGNR
  - 150-mA, 36-V, low-noise, high-PSRR adjustable LDO
  - Provides VAA (analog supply) adjustable between ~5V and 10V (up to 12V from a 12V input)
  - Product: [TPS7A4901DGNR](https://www.ti.com/product/TPS7A49/part-details/TPS7A4901DGNR)
  - Procurement: [Digikey](https://www.digikey.ca/short/hr5vft5w)
  - Datasheet: [TPS7A49 Datasheet](https://www.ti.com/lit/ds/symlink/tps7a49.pdf)
- **Clock Regulator:**
  - A second TPS7A4901 (configured to output a fixed 5V) provides the dedicated, low-noise 5VA rail for the external oscillator.
- **Grounding:**
  - Analog ground (gnd_a) is kept separate from digital ground, but tied at a star point.

### Analog Midsupply Voltage Source

- **Reference Buffer:** OP1177 (e.g., OP1177ARMZ)
  - Precision, low-noise op amp used to buffer a resistor divider (with a multi-turn pot) to generate a tunable mid-supply voltage (VREF)
  - Provides a high-impedance reference for the REF pins on the AD8426 amplifiers
  - Product: [OP1177](https://www.analog.com/en/products/op1177.html)
  - Procurement: [Mouser](https://mou.sr/3E6iI69)
  - Datasheet: [OP1177 Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/op1177_2177_4177.pdf)
- **Implementation:**
  - Typically one buffered reference can drive all REF pins if layout is good; otherwise, consider one per AD8426.

### Antialiasing Filtering

- Simple RC circuits

### External ADC

- **ADC:** ADS131M08IPBS
  - 24-bit, 32-kSPS, 8-channel simultaneous delta-sigma ADC
  - Product: [ADS131M08](https://www.ti.com/product/ADS131M08)
  - Procurement: [Mouser](https://mou.sr/4j7vdgE)
  - Datasheet: [ADS131M08 Datasheet](https://www.ti.com/lit/ds/symlink/ads131m08.pdf)

### Load Cell

- **MODEL 1914A FEMUR LOAD CELL**
- **Overview:**
  - Provides comprehensive measurements of forces acting on the upper leg just above the knee joint.
  - Features six measurement channels—three forces and three moments—each implemented as a full-bridge strain gauge circuit.
- **Excitation:**
  - Typical excitation is 10 V (nominal), up to 15 V max.
- **Additional Information:**
  - Detailed wiring, specifications, and calibration info are available in the separate Load Cell Datasheet.
  - See [load_cell_specs.md](./load_cell_specs.md) for complete specifications and wiring details (datasheet summary).

### SPI & Data Logging

- **SPI Data Capture:**
  - High-speed SPI data from the ADC is captured by the ATmega4809 (Nano Every)
  - Utilize DMA to transfer data to a buffer
- **Data Logging:**
  - Logging to an SD card (via a high-speed SPI/SDIO interface) and/or real-time USB streaming

## Style Guide

### KiCad Power Symbols Legend

| Power Symbol | Designation | Reference                                                                                                 |
| ------------ | ----------- | --------------------------------------------------------------------------------------------------------- |
| +12V         | 12V         | 12V Source Supply (from the 12V rail on the backplane)                                                    |
| GND          | GND         | Common Ground (from the 12V rail's GND on the backplane)                                                  |
| +5V          | 5V          | Digital 5V Supply (generated by the buck converter for digital circuits)                                  |
| +5VA         | 5VA         | Dedicated 5V Supply for the external clock oscillator (low-noise LDO output)                              |
| VAA          | VAA         | Analog Front-End Supply (adjustable, 5–10V, for sensor conditioning, amps)                                |
| VPP-->VMID   | VMID        | _Renamed VPP symbol to VMID._ Mid-Supply Reference Voltage (buffered via OP1177 for instrumentation amps) |
| GNDA         | AGND        | Analog Ground (for sensor, amps, ADC; tied at star point with DGND)                                       |
| GNDD         | DGND        | Digital Ground (connected to common ground through heatsink pad)                                          |

### KiCad Colors Legend

| Color  | Value    | Reference                                          |
| ------ | -------- | -------------------------------------------------- |
| Red    | red3     | Analog 5V Supply (e.g., for amps, ADC, references) |
| Brown  | orange1  | Analog Ground (sensor/amp/ADC ground)              |
| Orange | orange3  | Digital 5V Supply (MCU, digital circuits)          |
| Black  | black    | Digital Ground (MCU ground)                        |
| Pink   | magenta3 | 12V Source Supply (from 12V rail on backplane)     |
| Blue   | blue3    | Clock Signals (oscillator, ADC CLKIN, SPI SCLK)    |
| Green  | green3   | Analog Signals (amp outputs to ADC + inputs)       |
| Yellow | yellow3  | SPI Control (CS, SYNC/RESET, etc.)                 |
| Purple | #A020F0  | Special/Reference/Control Signals                  |
| Grey   | gray2    | Miscellaneous/Unassigned                           |
| Teal   | cyan2    | Template/Backplane Connections                     |
