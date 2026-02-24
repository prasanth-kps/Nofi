# NoFi — Developer Guide

This document covers setting up the development environment, project structure, and configuration options for the NoFi audio mesh network app.

For a full feature overview, see the [root README](../README.md).

---

## Prerequisites

- **Node.js** v18 or later
- A modern browser with Web Audio API support (Chrome, Edge, Safari, Firefox)
- **HTTPS or `localhost`** — the browser requires a secure context for microphone access

---

## Setup

```bash
# From the repo root
cd no-fi

# Install dependencies
npm install

# Start the dev server
npm run dev
```

The dev server runs at `http://localhost:5173`. Microphone access works on `localhost` without HTTPS.

---

## Available Scripts

| Command | Description |
|---|---|
| `npm run dev` | Start local development server with HMR |
| `npm run build` | Production build to `dist/` |
| `npm run preview` | Serve the production build locally |
| `npm run lint` | Run ESLint across the codebase |

---

## Project Structure

```
no-fi/
├── components/
│   ├── NoFiChat.tsx       Main application component
│   └── NoFiChat.css       Component styles
├── src/
│   ├── App.tsx            Root app — mounts NoFiChat
│   ├── App.css            Global app styles
│   ├── main.tsx           Vite entry point
│   ├── index.css          Base CSS reset
│   └── assets/
├── public/
│   └── logo.png           App logo
├── index.html
├── vite.config.ts
├── tsconfig.json
├── tsconfig.app.json
└── tsconfig.node.json
```

All the core logic — audio transmission, decoding, mesh relay, and AI — lives in `components/NoFiChat.tsx`.

---

## Protocol Configuration

The FSK protocol parameters are defined as constants near the top of `NoFiChat.tsx`:

```typescript
const PROTOCOL = {
  TONE_DURATION: 0.12,    // seconds — longer = more reliable, slower
  GAP_DURATION: 0.04,     // seconds — silence between tones
  THRESHOLD: 30,          // FFT magnitude threshold (10–50)
  SILENCE_TIMEOUT: 1500,  // ms — triggers message completion
  STEP_FREQ: 60,          // Hz per character step
};
```

**Tuning tips:**

- **Better reliability** → increase `TONE_DURATION` to 0.15, lower `THRESHOLD` to 20
- **Faster transmission** → decrease `TONE_DURATION` to 0.08, increase `STEP_FREQ` to 80
- **Noisy environment** → increase `THRESHOLD` to 40–50

---

## Modes

| Mode | Frequency Range | Notes |
|---|---|---|
| Audible | 1.2–3 kHz | Clearly audible; best range; good for demos |
| Stealth | 16–20 kHz | Near-ultrasonic; less noticeable; hardware-dependent |

Stealth mode requires speakers and microphones with a flat response above 16 kHz. Most laptop hardware works; cheap earbuds may not.

---

## Offline AI (Voice Input)

The voice input feature uses `whisper-tiny.en` via [Transformers.js](https://huggingface.co/docs/transformers.js), running entirely in the browser:

- ~40MB model download on first use (cached by the browser indefinitely)
- No server, no API key, no internet needed after the first load
- Falls back gracefully if the model fails to load

---

## Deployment

```bash
npm run build
```

The `dist/` folder is a static site — deploy to Vercel, Netlify, GitHub Pages, or any static host.

**Important:** Production deployments must use HTTPS. The Web Audio API and `getUserMedia` (microphone) are blocked on insecure origins in all modern browsers.

---

## Troubleshooting

**Microphone permission denied**
Ensure the page is served over `https://` or `localhost`. HTTP origins cannot access the microphone.

**No signal detected**
- Check that volume is high on the transmitting device
- Keep devices within ~10 feet (audible) or ~5 feet (stealth)
- Reduce background noise, or lower `THRESHOLD` in the protocol config

**Messages decode incorrectly**
Room echo and speaker distortion are the most common causes. Try increasing `TONE_DURATION` or moving devices to a quieter space.

**Stealth mode not working**
Test your hardware's frequency response with a tone generator. If it can't reproduce 16 kHz+, use Audible mode instead.

**AI model not loading**
The first load requires an internet connection to download the model (~40MB). After that it's fully cached. If it consistently fails, check that `jsdelivr.net` isn't blocked on your network.
