---
title: "Changelog"
description: "Hardware and firmware revision history."
---

# Changelog

## Hardware

### v0.2.0 (in review)
- The ADC inputs and supply are inside the ADS131M08's range: VMID is
  1.65 V from +3.3VADC, the ADC negative inputs return to VMID, and the
  ADC supply is 3.31 V (it was set to 5.6 V).
- Bridge excitation comes from an LT3045 (500 mA); JP6 selects 10 V or 5 V.
- LED and SYNC move off PB6/PB7, which carry I2C through SB16/SB18.
- All eight M12 connectors share one pinout.
- E-stop series resistor R31 and LED decoupling C40, from slice template r4.
- Excitation sense on PA3.

### v0.1.0 (initial)
- Initial schematic and PCB layout (4-layer)
- AD8426 PSG amplifier sub-circuit
- TPS7A4901 adjustable regulator sub-circuit
- Full-bridge load cell interface
