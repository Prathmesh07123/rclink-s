# RCLink-S — Custom RC Transmitter & Receiver with SBUS Output

RCLink-S is a custom-built RC transmitter–receiver system designed to understand
RC protocols, RF communication, and embedded firmware at a low level.

The system implements a complete RC data path:
**stick inputs → custom protocol → RF link → SBUS output → flight controller**.

This project focuses on understanding RC communication and protocol design at a low level, using an SBUS-based implementation chosen for availability and compatibility reasons.

---

## ✨ Key Features

- Multi-MCU architecture (Arduino Nano, ESP8266, ESP32)
- Custom binary UART protocol for RC data
- NRF24L01-based RF communication
- SBUS output compatible with INAV / Betaflight / ArduPilot
- Failsafe handling on receiver side
- Designed for low latency and reliability

---

## 🧠 High-Level Architecture

RCLink-S follows a modular, multi-MCU architecture where each stage
has a clearly defined responsibility in the RC data pipeline.

Data flow:
User Inputs (Sticks, Switches, Pots)
→ Arduino Nano (ADC processing & channel framing)
→ UART (Custom Binary Protocol)
→ ESP8266 (RF Transmitter)
→ NRF24L01 (250 kbps wireless link)
→ ESP32 (RF Receiver, packet validation & failsafe)
→ SBUS Encoder
→ Flight Controller (INAV / Betaflight / ArduPilot)

An architecture diagram will be added in a future update.