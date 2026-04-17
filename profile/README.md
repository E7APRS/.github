# E7APRS — Real-Time Position Tracking for Amateur Radio

E7APRS is a full-stack GPS tracking system that collects position data from APRS, DMR, and custom sources and streams
live updates to an interactive web map. It is designed to work **with or without internet connectivity** — the LoRa
relay module enables position forwarding over radio links when all other infrastructure is down.

## Why This Exists

Standard APRS tracking depends on internet-connected infrastructure (APRS-IS servers, iGates, cellular data). When that
infrastructure fails — natural disaster, power grid outage, overloaded mobile networks — tracking stops exactly when
it's needed most.

E7APRS solves this by combining multiple data paths into a single dashboard:

| Scenario             | Data Path                             | Internet Required        |
|----------------------|---------------------------------------|--------------------------|
| Normal operations    | APRS-IS TCP stream, APRS.fi API       | Yes                      |
| DMR radio with GPS   | Audio decode via DSD+                 | Yes (for RadioID lookup) |
| **Network failure**  | **LoRa relay (868 MHz, 2+ km range)** | **No**                   |
| Fixed infrastructure | Static station config                 | No                       |

The LoRa relay path is the critical differentiator: it operates independently of cellular, internet, or power grid
availability. A battery-powered Raspberry Pi with a LoRa module can forward positions from a remote repeater site to a
command center over a direct radio link.

## Use Cases

### Civil Protection

During floods, earthquakes, wildfires, mobile and internet networks are often the first to fail. E7APRS enables Civil
Protection coordination by providing:

- **Real-time unit tracking** — field teams carrying DMR radios with GPS are automatically tracked on a shared map,
  without any cellular infrastructure
- **Offline relay** — a LoRa link between the affected area and the operations center continues to forward positions
  when all other communication paths are down
- **Store & forward** — if even the LoRa link is temporarily interrupted (terrain, interference), positions are queued
  locally and forwarded automatically when the link recovers — no data is lost
- **Shared situational awareness** — the web dashboard shows all units on a single map with movement trails, speed,
  altitude, and timestamps

**Deployment example** — flood response:

```
[Affected area]                              [Operations center]
DMR radios (field teams)                     Web dashboard (map + device list)
    │                                              ▲
    ▼                                              │
Backend (Raspberry Pi) → LoRa TX  ~~~LOS~~~  LoRa RX → Backend → Frontend
    battery powered                           mains / generator
```

### Network Failure / Grid Down

When internet or power infrastructure fails, the system degrades gracefully:

1. **Internet down, power up** — APRS-IS and APRS.fi sources stop, but DMR and LoRa relay continue to deliver positions
2. **Internet + cellular down** — LoRa relay provides the last remaining position data path, operating on 868 MHz ISM
   band with no infrastructure dependency
3. **Power grid down** — a battery-powered repeater site with a LoRa module can continue forwarding positions to the
   command center for as long as the battery lasts. Battery life can be extended by using solar panels for charging

### Mountain / Field Operations

Amateur radio operators on mountain activations (SOTA, field days) can be tracked via DMR GPS or APRS, with positions
relayed to a base station over LoRa when no internet is available at the summit.

---

## System Architecture

In a split deployment, each site runs its own backend. The LoRa link bridges positions between the two without internet.

```
REPEATER SITE                                    COMMAND CENTER

┌──────────────┐                                 ┌──────────────┐
│ Data Sources │                                 │ aprs-frontend │
│ APRS-IS, DMR │                                 │ Leaflet map   │
└──────┬───────┘                                 │ Device list   │
       │                                         └──────▲───────┘
┌──────▼───────┐                                        │ WebSocket
│ aprs-backend │                                 ┌──────┴───────┐
│ REST + WS    │                                 │ aprs-backend │
│ SQLite       │                                 │ REST + WS    │
└──────┬───────┘                                 │ SQLite + Supa│
       │ webhook                                 └──────▲───────┘
┌──────▼───────┐                                        │ POST /api/relay
│ lora-relay   │                                 ┌──────┴───────┐
│ sender       │──── LoRa 868 MHz ──────────────>│ lora-relay   │
│ queue+encode │     (2+ km range)               │ receiver     │
└──────────────┘                                 └──────────────┘
```

---

## Projects

| Repository                                                         | Stack                                      | Port | Purpose                                                        |
|--------------------------------------------------------------------|--------------------------------------------|------|----------------------------------------------------------------|
| [aprs-backend](https://github.com/E7APRS/APRS-dashboard-backend)   | Node.js + TypeScript + Express + Socket.io | 3001 | Data ingestion, SQLite + Supabase storage, WebSocket broadcast |
| [aprs-frontend](https://github.com/E7APRS/APRS-dashboard-frontend) | Next.js 14 + React 19 + Leaflet + Tailwind | 3000 | Live map, device list, trail history, auth                     |
| [lora-relay](https://github.com/E7APRS/lora-relay)                 | Node.js + TypeScript + Express             | 3002 | Offline position sync over LoRa / TCP (store & forward)        |
| [DMR-parser](https://github.com/E7APRS/dmr-parser)                 | Node.js + TypeScript                       | —    | DMR audio decode (DSD+) to APRS position bridge                |

### Data Flow

```
GPS Sources → Backend → In-Memory Store + SQLite (+ Supabase backup) → Socket.io → Frontend
                  │
                  └→ Relay Webhook → NDJSON Queue → Binary Encode → LoRa TX
                                                                        │
                  ┌─────────────────────────────────────────────────────┘
                  └→ LoRa RX → Binary Decode → POST /api/relay → Store → Frontend
```

---

## Data Sources

### APRS-IS (`aprsis`)

Direct TCP connection to the APRS-IS Tier 2 network. Receives real-time position packets matching a server-side filter (
e.g. `p/E7` for all Bosnian callsigns). Parses uncompressed and compressed APRS position formats. Extracts symbol table
and code.

### APRS.fi (`aprsfi`)

Polls the APRS.fi HTTP API for a configured list of callsigns. ToS-compliant: correct User-Agent, exponential backoff,
minimum 10s poll interval.

### DMR (`dmr`)

HD1 radio connected via audio cable to PC. DSD+ decodes digital audio, DMR-parser extracts GPS + DMR-ID, resolves
callsign via RadioID.net, and POSTs to backend. Only transmissions with GPS data are forwarded.

### LoRa Relay (`relay`)

Positions forwarded from a remote backend over an internet-independent link. Binary codec compresses each position to ~
35 bytes. A 128-byte LoRa packet fits 3 positions. NDJSON queue survives process restarts and extended link outages. Two
serial modes: transparent (T3 LoRa32) and AT command (RYLR896).

### Fixed Stations

Statically configured known infrastructure (repeaters, club stations). Always active, broadcast every 30 seconds.

---

## LoRa Relay — Offline Position Sync

The relay system enables position forwarding between two sites without internet. Compact binary encoding (~35
bytes/position) is optimized for low-bandwidth LoRa links.

| Feature      | Detail                                           |
|--------------|--------------------------------------------------|
| Encoding     | Compact binary, ~35 bytes per position           |
| Batch size   | 3 positions per 128-byte LoRa packet             |
| Queue        | Persistent NDJSON file, survives restarts        |
| Retry        | Exponential backoff on transport failure         |
| Transports   | Mock (dev), TCP (LAN), Serial/LoRa (production)  |
| Serial modes | Transparent (T3 LoRa32) or AT commands (RYLR896) |
| Range        | 2+ km line-of-sight at 868 MHz, 100 mW           |

```bash
# Repeater site (sender)
RELAY_MODE=sender RELAY_TRANSPORT=serial SERIAL_PATH=/dev/ttyUSB0 npm run dev

# Command center (receiver)
RELAY_MODE=receiver RELAY_TRANSPORT=serial SERIAL_PATH=/dev/ttyUSB0 npm run dev
```

---

## Quick Start

```bash
# 1. Backend
cd aprs-backend
cp .env.example .env          # fill in credentials
npm install && npm run dev    # http://localhost:3001

# 2. Frontend
cd aprs-frontend
cp .env.local.example .env.local
npm install && npm run dev    # http://localhost:3000

# 3. DMR bridge (optional — requires DSD+ and HD1 radio)
cd DMR-parser
cp .env.example .env
npm install
dsdplus.exe -i 0 | npm run dev

# 4. LoRa relay (optional — for split repeater/command-center deployment)
cd lora-relay
cp .env.example .env
npm install && npm run dev
```

### Docker (all-in-one backend + frontend)

```bash
docker-compose up --build     # http://localhost
```

---

## REST API

| Method | Endpoint                          | Description                          |
|--------|-----------------------------------|--------------------------------------|
| GET    | `/api/status`                     | Active sources, feature flags        |
| GET    | `/api/devices`                    | All known devices                    |
| GET    | `/api/devices/:radioId`           | Single device                        |
| GET    | `/api/positions/latest`           | Latest position per device           |
| GET    | `/api/positions/:radioId/history` | Position history (limit=500)         |
| POST   | `/api/gps`                        | Manual position push (DMR bridge)    |
| POST   | `/api/relay`                      | Relay batch ingest (direct to store) |

## WebSocket Events

| Event                | Direction       | Payload                                       |
|----------------------|-----------------|-----------------------------------------------|
| `positions:snapshot` | server → client | `Position[]` — full state on connect          |
| `history:snapshot`   | server → client | `Record<radioId, Position[]>` — trail history |
| `position:update`    | server → client | `Position` — live update                      |

---

## Tech Stack

| Component       | Technology                                      |
|-----------------|-------------------------------------------------|
| Backend         | Node.js 20, TypeScript, Express, Socket.io      |
| Frontend        | Next.js 14, React 19, Leaflet, Tailwind CSS     |
| Database        | SQLite (primary) + Supabase PostgreSQL (backup) |
| Auth            | Supabase Auth (email/password + Google OAuth)   |
| Relay codec     | Custom binary, ~35 bytes/position               |
| Relay transport | LoRa 868 MHz (SerialPort), TCP, mock            |
| DMR decode      | DSD+ (external) piped to Node.js parser         |
| Deployment      | Docker (multi-stage) + nginx reverse proxy      |

---

## Contributions

If you find this project interesting and would like to contribute, you're very welcome - whether you're a developer,
radio amateur, or just curious about APRS/DMR systems.

### Ways to Contribute

* Radio integrations - decoding improvements, hardware experimentation
* Improve frontend - map UX, performance, responsiveness
* Optimize backend - data handling, scaling, caching strategies
* Suggest features - especially around core architecture and data flow
* Report bugs - things may break often in this stage, feedback is extremely valuable

> ⚠️ Project is in Pre-Alpha
> This project is in a very early stage of development.
> Expect breaking changes, incomplete features, unstable APIs, and frequent refactoring.

### Getting Started

#### 1. Fork the repository you want to contribute to:

Backend: https://github.com/E7APRS/APRS-dashboard-backend
Frontend: https://github.com/E7APRS/APRS-dashboard-frontend
DMR Parser: https://github.com/E7APRS/dmr-parser

#### 2. Create a feature branch:

git checkout -b feature/your-feature-name

#### 3. Make your changes following project conventions:

TypeScript-first
Clean, modular structure (SOLID, DRY)
Expect ongoing refactors — keep code adaptable

#### 4. Commit with clear messages:

git commit -m "feat: add APRS symbol filtering"

#### 5. Push and open a Pull Request with a clear description

### Contribution Guidelines

- Keep PRs focused and small
- Include context and reasoning
- Be prepared for requested changes or refactors due to evolving architecture
- Update documentation when relevant
- Test your changes locally before submitting

### Important Notes for Pre-Alpha Contributors

- APIs and data structures may change without notice
- Backend/frontend contracts are not stable yet
- Some features may be partially implemented or experimental
- Refactoring is frequent and expected

## License

Copyright (c) 2026 Adin Beslagic. All rights reserved.
Reuse permitted only with written permission from the author.
See [LICENSE](https://github.com/E7APRS/.github/tree/master?tab=License-1-ov-file) for full terms.
