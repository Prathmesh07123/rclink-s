# RCLink-S System Architecture

## Overview

RCLink-S is designed as a modular RC transmitter–receiver system where each
hardware stage has a clearly defined responsibility in the RC data pipeline.

Instead of using a single MCU to handle all tasks, the system separates
input processing, RF transmission, RF reception, and SBUS generation across
dedicated microcontrollers. This separation improves reliability, simplifies
debugging, and mirrors the architecture used in professional RC systems.

---

## High-Level Data Flow

The complete RC data path is shown below:

User Inputs  
(Sticks, Switches, Potentiometers)  
↓  
Arduino Nano  
(ADC Processing, Calibration, Channel Framing)  
↓ UART (Custom Binary Protocol)  
ESP8266  
(RF Transmitter Controller)  
↓ SPI  
NRF24L01 (250 kbps RF Link)  
↓  
ESP32  
(RF Receiver, Packet Validation, Failsafe Logic, SBUS Framing)  
↓
Flight Controller  
(INAV / Betaflight / ArduPilot)

---

## MCU Responsibilities

### Arduino Nano — Input Processing Unit

The Arduino Nano acts as the primary input processor inside the transmitter.

Responsibilities:
- Read analog stick, switch, and potentiometer inputs
- Perform stick calibration and normalization
- Map raw ADC values to RC channel ranges
- Construct RC channel frames
- Transmit framed channel data over UART

This MCU is intentionally limited to input-related tasks to ensure deterministic
sampling and predictable timing.

---

### ESP8266 — RF Transmitter Controller

The ESP8266 functions as an external RF transmitter module, similar to
commercial JR-style RC transmitter modules.

Responsibilities:
- Receive RC frames from Arduino Nano over UART
- Validate incoming frame structure
- Forward RC data to the NRF24L01 via SPI
- Handle RF configuration (data rate, channel, retries)

Separating RF transmission from input processing allows RF parameters
to be tuned independently of stick sampling logic.

---

### ESP32 — RF Receiver & SBUS Generator

The ESP32 serves as the receiver-side controller.

Responsibilities:
- Receive RC packets from the NRF24L01
- Validate packets using sequence
- Detect packet loss and apply failsafe logic
- Convert RC channel data into SBUS frames
- Output SBUS signal to the flight controller

The ESP32 was selected for its higher performance and reliable UART timing,
which are important for stable SBUS generation.

---

## RF Link Layer

RCLink-S uses the NRF24L01 2.4 GHz transceiver for wireless communication.

Key RF characteristics:
- Data rate: 250 kbps (range-prioritized)
- SPI-based interface
- Configurable retries and packet acknowledgment
- Point-to-point communication model

The RF layer is intentionally kept simple to allow clear analysis of latency,
packet loss, and reliability.

---

## Design Rationale

Key architectural decisions include:

- **Modular MCU separation**  
  Prevents RF processing from affecting input sampling stability.

- **External transmitter concept**  
  Mirrors real-world RC transmitter module designs.

- **Custom binary protocol**  
  Enables efficient RC data transmission with minimal overhead.

- **SBUS output**  
  Ensures compatibility with widely used flight controllers.

---

## Future Architecture Extensions

The current architecture allows future upgrades without major redesign:

- Higher-rate RF protocols
- Telemetry return path
- CRSF or ELRS-style packet structures
- OTA firmware updates
- Additional receiver output formats

These extensions can be layered on top of the existing architecture.
