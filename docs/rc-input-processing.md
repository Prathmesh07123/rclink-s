# RCLink-S — RC Input Processing & Channel Mapping

This document explains how RCLink-S processes raw input signals
from sticks, switches, and potentiometers on the Arduino Nano.

Although the final firmware integrates framing and communication,
this document focuses only on **input handling and normalization**.

---

## 🎯 Design Goals

- Deterministic RC values
- Stable center position
- Noise-resistant ADC behavior
- Consistent channel ordering

---

## 🧠 ADC Strategy

- ADC reference: **External AREF (3.3V)**
- Resolution: 10-bit (0–1023)
- All analog inputs powered from the same 3.3V rail

This ensures:
- Reduced noise
- Repeatable calibration
- Stable mid-point detection

---

## 📐 Stick Calibration (EEPROM-Based)

Each stick axis stores calibration data in EEPROM:

- Minimum
- Center
- Maximum

This allows:
- Mechanical tolerance compensation
- No recalibration on every boot
- Persistent configuration

Calibration structure:
- Roll: min / center / max
- Pitch: min / center / max
- Yaw: min / center / max
- Throttle: min / max (no center)

---

## 🎯 RC Value Mapping

All RC channels are normalized to standard RC ranges:

| Type | Output Range |
|----|-------------|
| Analog axes | 1000–2000 |
| Digital switches | 1000 / 1500 / 2000 |

A deadband is applied around the center position to prevent jitter.

---

## 🔀 Channel Assignment

| Channel | Function |
|------|----------|
| CH1 | Roll |
| CH2 | Pitch |
| CH3 | Throttle |
| CH4 | Yaw |
| CH5–CH12 | AUX switches & pots |

This ordering is preserved through the entire system,
including UART framing and SBUS generation.

---

## ✅ Summary

RC input processing in RCLink-S is designed to behave like
a professional RC transmitter, not a hobby prototype.

All higher-level features build on this foundation.
