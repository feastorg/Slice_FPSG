---
title: "Architecture"
description: "Hardware and firmware architecture overview."
---

# Architecture

## Hardware

FPSG is an 8-channel full-bridge strain-gauge slice on a 4-layer board. It
excites each bridge, amplifies it with an AD8426 instrumentation amplifier,
and converts all eight channels at once with an ADS131M08 24-bit ADC. A
NUCLEO-L432KC, in the slice's Nano-format socket, reads the ADC over SPI,
logs to a microSD card and talks to the bus over I²C.

### Signal chain, per channel

| Stage | Part | Notes |
|---|---|---|
| Bridge connector | J2–J9, Phoenix 1439890 M12 | 1 SIG+, 2 SIG−, 3 EXC− (GNDA), 4 EXC+ (VAA), 5 and shell GNDA |
| Excitation | U8, LT3045 | VAA = 10.0 V (JP6 1–2, default) or 4.99 V (JP6 2–3); 500 mA |
| Amplifier | U4–U7, AD8426 (two per package) | G = 1 + 49.4 kΩ / Rg; REF = VMID |
| Anti-alias filter | R1–R16, C5–C12 | 1 kΩ per leg, 10 nF differential |
| ADC | IC1, ADS131M08 | ±1.2 V full scale at gain 1; AINxN returns to VMID |

VMID is 1.65 V: +3.3VADC halved by R17/R18 and buffered by U3A (MCP6022).
Each amplifier output sits at VMID ± 1.2 V, inside the ADC's input range,
and each ADC negative input returns to VMID through its filter resistor, so
the ADC reads the amplifier output relative to its reference.

Rg is left to the build. At 10 V excitation, about 1.08 V at full load:

| Bridge sensitivity | Full-scale signal | Gain | Rg (E96) |
|---|---|---|---|
| 1.2 mV/V | 12 mV | 88.9 | 562 Ω |
| 1.5 mV/V | 15 mV | 71.8 | 698 Ω |
| 1.8 mV/V | 18 mV | 59.5 | 845 Ω |
| 2.6 mV/V | 26 mV | 41.8 | 1.21 kΩ |

At 5 V excitation the signal halves; double the gain. The AD8426's
internal-node limits (datasheet Table 12, Equations 1–3) hold with margin for
both excitation voltages at these gains.

### Power

| Rail | Source | Feeds |
|---|---|---|
| +12V | Bus J1 pins 2 and 9 | U1, U8, U9 |
| +5VD | U1, AP63205 buck | Nucleo +5V, U3, LED |
| +3V3 | Nucleo regulator | ADC DVDD, microSD |
| +3.3VADC | U9, TPS7A4901, 3.31 V | ADC AVDD, VMID divider |
| VAA | U8, LT3045 | Bridges and amplifiers |

GNDA joins GNDPWR at net ties NT2 and NT3, under the two regulators; GNDD
joins GNDPWR at NT1.

### MCU pins (NUCLEO-L432KC)

| Pin | Net | Use |
|---|---|---|
| A4, A5 (PA5, PA6) | I2C_DAT, I2C_CLK | Bus I²C. SB18/SB16 (on by default) join them to PB7/PB6, the STM32's I2C1 pins; keep PA5/PA6 floating inputs |
| PA12 (D2) | ESTOP_MCU | Bus e-stop through R31 (1 kΩ) |
| PB0 (D3) | INT | Bus interrupt |
| PA4 (A3) | SYNC | Bus sync |
| PA7 (A6) | LED | WS2812B data |
| PB3, PB4, PB5 | SCK, MISO, MOSI | SPI1: ADC and microSD |
| PB1 (D6) | ADC_CS | ADC chip select |
| PA11 (D10) | SD_CS | microSD chip select |
| PC15 (D8) | DRDY | ADC data ready |
| PC14 (D7) | RST_ADC | ADC SYNC/RESET |
| PA8 (D9) | CLK_MCO | ADC CLKIN, from the MCU clock output |
| PA0 (A0) | VMID_SENS | VMID through R19 |
| PA1 (A1) | VADC_SENS | +3.3VADC, buffered by U3B |
| PA3 (A2) | EXC_SENS | VAA × 10 k / 40.1 k (2.49 V at 10 V), filtered by C41 |

The ADC's clock must stay within 0.3–8.4 MHz (ADS131M08 datasheet,
recommended operating conditions).

## Firmware

_Not yet developed._
