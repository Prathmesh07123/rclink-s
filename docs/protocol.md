# RCLink-S

RCLink-S is a low-latency RC data transport system that moves RC channel data from an **Arduino Nano** to an **ESP8266 RF transmitter** using a custom, deterministic UART protocol.

This repository contains the protocol specification, reference implementations, and tooling to generate, test, and validate frames.

---

## 🚀 Features

- Custom **binary UART protocol** (deterministic & low latency)
- Fixed-size frames for predictable timing
- CRC16-CCITT error detection
- Sequence numbering for dropped-frame detection
- Embedded-friendly (Arduino / ESP-class MCUs)
- Forward-compatible framing

---

## 📄 Protocol Specification

The full protocol definition lives here:

```
docs/protocol.md
```

**Quick summary:**

- Baud rate: **115200**
- Frame rate: **250 Hz (4 ms)**
- Frame size: **30 bytes**
- Payload: **12 RC channels (uint16, little-endian)**
- CRC: **CRC16-CCITT (0x1021, init 0xFFFF)**

---

## 📦 Frame Layout

| Field | Size | Description |
|------|------|-------------|
| SOF0 | 1 | 0xAA |
| SOF1 | 1 | 0x55 |
| LEN  | 1 | Payload length (24) |
| SEQ  | 1 | Frame counter |
| PAYLOAD | 24 | 12 × uint16 RC channels |
| CRC | 2 | CRC16-CCITT |

CRC is calculated over:

```
LEN + SEQ + PAYLOAD
```

---


## 🔌 Embedded Integration

### Arduino (Transmitter Side)

- Pack RC channel values into uint16_t (1000–2000)
- Send frames every **4 ms**
- Increment SEQ on each frame

### ESP8266 (Receiver Side)

- Scan for `0xAA 0x55`
- Validate LEN
- Verify CRC
- Track SEQ gaps
- Forward channels to RF / SBUS layer

---

## 🛠 Debugging Tips

- Log raw UART bytes and decode using the Python tool
- Watch SEQ counter for dropped frames
- CRC failures usually indicate baud mismatch or buffer overruns
- SOF resync handles noisy links automatically

---

## 📁 Repository Layout

```
.
├── docs/
│   └── protocol.md
├── tools/
│   └── rclinks_frame.py
├── firmware/
│   ├── arduino/
│   └── esp8266/
└── README.md
```

---

## 📜 License

MIT License — use freely in open or closed-source projects.

---

## ✨ Status

✅ Protocol stable

🚧 Firmware and tooling under active development

---

**RCLink-S** is designed to be boring, predictable, and fast — exactly what RC control links should be.

