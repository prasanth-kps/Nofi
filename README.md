<div align="center">

<img src="no-fi/public/logo.png" alt="NoFi Logo" width="120" />

# NoFi

### Communicate when nothing connects.

*A peer-to-peer audio modem that transmits data through sound waves —*
*no WiFi, no cellular, no internet required.*

[![Live Demo](https://img.shields.io/badge/Live%20Demo-no--fi.vercel.app-06b6d4?style=for-the-badge&logo=vercel&logoColor=white)](https://no-fi.vercel.app)

[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)](https://reactjs.org/)
[![Vite](https://img.shields.io/badge/Vite-646CFF?style=flat-square&logo=vite&logoColor=white)](https://vitejs.dev/)
[![License: MIT](https://img.shields.io/badge/License-MIT-22c55e?style=flat-square)](https://opensource.org/licenses/MIT)

</div>

---

## What is NoFi?

NoFi is an **offline audio mesh network** built entirely in the browser. It encodes text into audio tones using **Frequency Shift Keying (FSK)** and transmits them through your device's speakers — other devices pick them up via their microphone, decode the signal, and optionally relay it further. No server. No internet. Just sound.

Originally built as a proof-of-concept for disaster-resilient communication, it also features offline voice-to-text powered by a locally-run Whisper AI model, GPS coordinate broadcasting, and a stealth near-ultrasonic mode.

---

## Features

**Audio Transmission**
- FSK encoding across human-audible (1.2–3 kHz) and near-ultrasonic (16–20 kHz) bands
- Real-time FFT-based decoding with a state-machine receiver
- Collision detection and smart timing to avoid transmission conflicts

**Mesh Networking**
- Devices auto-relay received messages after a random 10–20s delay
- Duplicate message filtering via ID system prevents relay loops
- Organically extends range across multiple devices

**Offline AI**
- Voice-to-text via `whisper-tiny.en` running 100% locally via Transformers.js
- ~40MB one-time download, works fully offline after that

**Emergency Tools**
- One-click GPS coordinate broadcast
- No infrastructure dependency — works when everything else is down

**UI**
- Glassmorphism design with live audio spectrum visualizer
- Signal strength meter and relay queue manager
- Fully responsive across desktop, tablet, and mobile

---

## Protocol

NoFi uses **FSK modulation** — each character maps to a distinct frequency:

```
Message: "Hi"
    ↓
[Marker][H][Marker][i]
    ↓
1200 Hz → 3620 Hz → 1200 Hz → 4220 Hz
    ↓
Transmitted via speakers → Received via microphone → FFT → Decoded
```

| Parameter | Audible Mode | Stealth Mode |
|---|---|---|
| Marker Frequency | 1.2 kHz | 16 kHz |
| Base Frequency | 1.5 kHz | 16.5 kHz |
| Step Size | 60 Hz/char | 40 Hz/char |
| Tone Duration | 120 ms | 120 ms |
| Gap Duration | 40 ms | 40 ms |
| Bandwidth | ~7 kHz | ~4 kHz |

Message format: `[MessageID]|[MessageText]`  — e.g., `A1B2C3D4|Hello World`

---

## Architecture

```
NoFiChat (Root Component)
│
├── Audio Engine
│   ├── Transmitter      FSK encoder → Web Audio API oscillators
│   ├── Receiver         AnalyserNode → FFT → frequency detection
│   └── Decoder          State machine: IDLE → WAIT_MARKER → READ_CHAR
│
├── Mesh Network
│   ├── Message Queue    Pending relay buffer
│   ├── Relay Logic      Random delay broadcast
│   └── Duplicate Filter ID-based deduplication
│
├── AI Pipeline
│   ├── Model Loader     Transformers.js whisper-tiny.en
│   ├── Audio Recorder   MediaRecorder API
│   └── Transcription    Local inference, no server
│
└── UI
    ├── Onboarding Modal
    ├── Chat Interface
    ├── Relay Queue Panel
    └── Status Indicators
```

**Stack:** React 18 · TypeScript · Vite · Tailwind CSS · Web Audio API · Transformers.js

---

## Getting Started

> Requires HTTPS (or `localhost`) for microphone access.

```bash
git clone https://github.com/prasanth-kps/Nofi.git
cd Nofi/no-fi
npm install
npm run dev
```

Open `http://localhost:5173`, click **Activate Modem**, and allow microphone access.

To test transmission between two devices, open the app on both, type a message on one, and watch it decode on the other within range (~3–10 meters indoors).

---

## Performance

| Metric | Value |
|---|---|
| Transmission Speed | ~6 characters/second |
| Effective Bitrate | ~48 bps (audible) / ~32 bps (stealth) |
| Indoor Range | 3–10 meters |
| Encoding Latency | ~200 ms |
| Decoding Latency | ~150 ms |
| Reliability | 95%+ in quiet environments |
| AI Model Size | 40 MB (one-time download) |
| App Bundle Size | ~150 KB (excluding AI model) |

---

## Roadmap

**v1.1**
- [ ] CRC/checksum error correction
- [ ] Adaptive frequency selection
- [ ] Multi-channel transmission
- [ ] File transfer support
- [ ] PWA offline support

**v2.0**
- [ ] End-to-end encryption (ECDH)
- [ ] Digital signatures
- [ ] Compressed transmission
- [ ] Network topology visualization

---

## Security Notice

NoFi v1.0 transmits in **plaintext**. It is intended for educational use, demos, and experimental mesh networking. Do not use it for sensitive, private, or safety-critical communications.

---

## Built by

| | |
|---|---|
| [Prasanth KPS](https://github.com/prasanth-kps) | [@prasanth-kps](https://github.com/prasanth-kps) |
| [Karthik Red](https://github.com/KarthikRed2000) | [@KarthikRed2000](https://github.com/KarthikRed2000) |

---

## License

[MIT](LICENSE) — free to use, modify, and distribute.

---

<div align="center">

[Live Demo](https://no-fi.vercel.app) · [Report a Bug](https://github.com/prasanth-kps/Nofi/issues) · [Request a Feature](https://github.com/prasanth-kps/Nofi/issues)

</div>
