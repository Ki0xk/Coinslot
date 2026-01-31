# Ki0xk — Physical Coin Acceptor Module

Arduino Uno firmware for the physical coin acceptor used in Ki0xk, a physical payments infrastructure for events, festivals, retail, and temporary venues.

> ⚠️ **This project is not a crypto ATM.**
> It is a physical interface for modern payment infrastructure.

## Overview

This hardware module represents the physical entry point of the Ki0xk system, allowing users to load value into a digital session using real-world cash.

The Arduino Uno:
- Detects and validates inserted coins
- Translates coin pulses into value units
- Sends structured JSON signals to the Ki0xk software layer

## How It Fits Into Ki0xk

```
Ki0xk Kiosk → physical entry point
     ↓
Coins/Cash → initial value source
     ↓
Arduino Module → signal + validation layer (this repo)
     ↓
NFC/Session → user interaction bridge
     ↓
USDC → value standard (software layer)
     ↓
Yellow → instant off-chain settlement engine
```

This repository focuses only on the hardware side of that flow.

## Hardware Requirements

- Arduino Uno (ATmega328P)
- Electronic coin acceptor (pulse-based)
- Coin acceptor signal connected to **Pin 2** (interrupt-capable)

## Wiring

| Coin Acceptor | Arduino Uno |
|---------------|-------------|
| COIN (signal) | Pin 2       |
| GND           | GND         |
| VCC           | 5V or 12V (per acceptor spec) |

## Installation

1. Install [PlatformIO](https://platformio.org/install)
2. Clone this repository
3. Build and upload:

```bash
platformio run -e uno --target upload
```

4. Monitor output:

```bash
platformio device monitor -b 115200
```

## Output Format

The module emits JSON over serial at 115200 baud:

```json
{"type":"coin","pulses":13,"value":5,"ok":true}
```

| Field | Description |
|-------|-------------|
| `type` | Always `"coin"` |
| `pulses` | Raw pulse count from acceptor |
| `value` | Mapped denomination (1, 2, or 5 peso) |
| `ok` | `true` if valid, `false` if unrecognized pulse count |

## Coin Classification

| Pulse Count | Value |
|-------------|-------|
| 1–2         | 1 peso |
| 5–10        | 2 peso |
| 11–16       | 5 peso |

Pulse ranges can be adjusted in `src/main.cpp` to match your coin acceptor's configuration.

## What This Repo Does

- ✅ Reads pulses from standard electronic coin acceptors
- ✅ Maps pulse counts to predefined denominations
- ✅ Prevents double-counting with hardware debouncing
- ✅ Emits clean, structured events to host system

## What This Repo Does NOT Do

- ❌ No blockchain logic
- ❌ No wallet management
- ❌ No cryptographic operations
- ❌ No payment settlement

All financial logic lives in the Ki0xk software layer. This module is pure hardware I/O.

## Use Cases

- Festival or event Ki0xk stations
- Retail or pop-up shops
- Temporary venues
- Cash-to-digital onboarding points
- Hybrid cash + NFC payment flows

## Architecture Philosophy

| Principle | Description |
|-----------|-------------|
| **Simplicity** | Arduino Uno–compatible, minimal dependencies |
| **Reliability** | Deterministic pulse counting and validation |
| **Interoperability** | Clean signals for any higher-level system |

The hardware is designed to be easy to replace, easy to audit, and easy to deploy at scale.

## Tunable Parameters

Located in `src/main.cpp`:

| Parameter | Default | Description |
|-----------|---------|-------------|
| `DEBOUNCE_US` | 2500 | Noise filtering threshold (microseconds) |
| `COIN_GAP_MS` | 240 | Silence duration indicating coin completion (ms) |
| `COIN_PIN` | 2 | GPIO pin for coin acceptor signal |

## Disclaimer

This repository is part of a hackathon prototype and reference implementation.

It demonstrates:
- How physical cash input can be bridged into modern digital payment systems
- How legacy hardware can coexist with instant, session-based payment infrastructure

**It is not intended for production use without further hardening.**

## License

MIT
