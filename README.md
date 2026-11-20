# Slice FPSG

Full-bridge strain-gauge slice for BREAD.

## Introduction

FPSG reads eight full-bridge strain gauges, such as the six channels of a
1914A femur load cell. Each bridge is excited from a low-noise 10 V or 5 V
supply, amplified by an AD8426, and converted with an ADS131M08 24-bit,
8-channel simultaneous-sampling ADC. A NUCLEO-L432KC reads the ADC, logs to
microSD and talks to the backplane over I²C.

## Usage

- Fit Rg1–Rg8 for the bridges' sensitivity; [the architecture
  page](docs/architecture.md) has a table.
- JP6 sets the excitation: 1–2 bridged (default) is 10 V; cut it and bridge
  2–3 for 5 V.
- Bridges connect to J2–J9 (M12): 1 SIG+, 2 SIG−, 3 EXC−, 4 EXC+.
- Leave solder bridges SB16 and SB18 on the Nucleo on (the default): bus
  I²C reaches the STM32 through them.

## Documentation

Please refer to the [docs](/docs) for more info.

---

<div align="center">
  <p>
    <img src="https://img.shields.io/badge/Developed_by-UWO--FAST--et--al-orange" alt="Developed by UWO-FAST et al.">
    <img src="https://img.shields.io/badge/Powered_by-Open_Source-blue" alt="Powered by Open Source">
  </p>
</div>
