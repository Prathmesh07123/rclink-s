# RCLink-S — Hardware Wiring Guide

This document describes the hardware wiring for the RCLink-S transmitter
input module based on the Arduino Nano.

The focus is on reliable reading of analog sticks, switches, and
potentiometers for RC input processing.

---

## 🧠 Overview

The Arduino Nano is responsible for:
- Reading analog stick positions
- Reading switches and auxiliary controls
- Providing stable and calibrated RC input data to the system

All input devices are directly connected to the Nano.

---

## 🔌 Power Distribution

- Arduino Nano operates at **5V**
- All analog sticks and potentiometers are powered from the 3.3V rail
- **AREF pin is externally tied to 3.3V**
- ADC reference is configured as **external (AREF)** in firmware
- All grounds are connected to a common GND

> ⚠️ Proper grounding is critical to reduce ADC noise.

---

## 🕹️ Stick Wiring (Analog Inputs)

Each RC stick axis is connected to an ADC-capable pin.

| Function | Arduino Nano Pin |
|--------|------------------|
| Roll (X) | A4 |
| Pitch (Y) | A3 |
| Throttle | A2 |
| Yaw | A1 |

**Wiring per axis:**
- VCC → 3.3V
- GND → GND
- Signal → Analog pin (A4–A1)

---

## 🎛️ Auxiliary Potentiometers

Auxiliary potentiometers are connected in the same manner as stick axes.

| Function | Arduino Nano Pin |
|--------|------------------|
| AUX7 Pot | A5 |
| AUX8 Pot | A6 |

These can be mapped to auxiliary RC channels.

---

## 🔘 Switch Inputs (Digital)

RCLink-S supports multiple switch types including 2-position,
3-position, and push buttons.

**2-Position Switches**

| Function | Arduino Nano Pin |
|--------|------------------|
| ARM Switch (AUX1) | D4 |
| AUX3 | D5 |

---

**3-Position Switches**

3-position switches are implemented using two digital inputs

| Function | HIGH Pin | LOW Pin |
|--------|--------|--------|
| AUX2| D3 | D8 |
| AUX4 | D9 | D10 |

This approach avoids analog multiplexing and ensures clean state detection.

---

**Push Buttons**

| Function | Arduino Nano Pin |
|--------|------------------|
| AUX5 | D11 |
| AUX6 | D12 |

Push buttons are treated as momentary AUX channels.

---

### Switch Configuration
- Configured using internal pull-up resistors
- Active-low logic (pressed = LOW)

This reduces external component count and improves reliability.

---

## 🔄 Channel Order Definition

RCLink-S uses a fixed internal channel ordering to maintain consistency
across the entire system.

| Channel | Assignment |
|--------|------------|
| CH1 | Roll |
| CH2 | Pitch |
| CH3 | Throttle |
| CH4 | Yaw |
| CH5 | AUX1 (ARM) |
| CH6 | AUX2 |
| CH7 | AUX3 |
| CH8 | AUX4 |
| CH9 | AUX5 |
| CH10 | AUX6 |
| CH11 | AUX7 |
| CH12 | AUX8 |

This ordering is preserved during protocol framing and SBUS generation.

---

## 🔔 User Feedback Interfaces

RCLink-S includes basic user feedback hardware:

| Component | Arduino Nano Pin |
|--------|------------------|
| Status LED | D7 |
| Passive Buzzer | D6 |

The buzzer is used for:
- Startup indication
- Calibration feedback
- Stick movement warnings

---

## 📐 Signal Conditioning Considerations

- Short wire lengths are recommended for analog signals
- Optional RC filtering can be added if required
- ADC values are filtered and normalized in firmware

Deadband logic is applied in software to prevent stick jitter.

---

## 📌 Notes

- This wiring layout ensures deterministic ADC behavior
- Channel mapping logic is documented in `docs/rc-input-processing.md`
- Communication protocol details are covered in separate documentation

