# E7APRS Tracker System — Deep Research on Appliance

## 1. Primary Domain: Amateur Radio Position Tracking (APRS)

The system is a **multi-source APRS aggregator and visualizer** purpose-built for the E7 (Bosnia & Herzegovina) amateur
radio community. It ingests positions from:

| Source             | Protocol                      | Latency             | Internet Required         |
|--------------------|-------------------------------|---------------------|---------------------------|
| **APRS-IS**        | TCP stream (rotate.aprs2.net) | Real-time (~1s)     | Yes                       |
| **APRS.fi**        | HTTP polling (15s intervals)  | Near-real-time      | Yes                       |
| **DMR**            | DSD+ audio decode → POST      | ~2-5s               | Yes (for APRS-IS forward) |
| **LoRa Relay**     | Binary codec over 868MHz      | 5-15s (batch cycle) | **No**                    |
| **Fixed Stations** | Hardcoded (E70AB, E74BMN)     | Static              | No                        |

This makes it a **unified tracking dashboard** — an operator sees every station on one map regardless of how the
position was transmitted.

---

## 2. Civil Protection & Disaster Response (Core Differentiator)

The most distinctive appliance is **offline-capable position tracking for emergency operations**. This is where the
system goes beyond what existing APRS tools (aprs.fi, YAAC, Xastir) offer.

### Scenario: Earthquake or flood cuts internet to a repeater site

```
Repeater Site (no internet)          Command Center (has internet)
┌────────────────────┐               ┌──────────────────────┐
│ DMR radios heard   │               │ Backend + Frontend   │
│ → DMR-parser       │               │ running normally     │
│ → Backend (local)  │               │                      │
│ → Relay Sender     │  LoRa 868MHz  │ Relay Receiver       │
│   queue persists   │ ─────────── → │ → POST /api/relay    │
│   until link up    │  2+ km range  │ → Live map update    │
└────────────────────┘               └──────────────────────┘
```

### Key capabilities for this scenario

- **Store & forward**: NDJSON queue survives process restarts and extended outages — positions accumulate for days if
  needed
- **Compact binary codec**: ~35 bytes/position fits 3 positions per 128-byte LoRa packet — critical at 9600 baud
- **Transport abstraction**: Same code works over LoRa serial, TCP (LAN), or mock (testing) — swap via env var
- **Direct store bypass**: Relay positions skip APRS-IS forwarding (which requires internet), going straight to the
  database and WebSocket broadcast
- **Duplicate detection**: Sequence numbers prevent position storms when the sender retries after brief disconnects

This is directly applicable to **Civilna zastita (Civil Protection)** operations in BiH, where mountainous terrain and
infrastructure fragility make internet connectivity unreliable during emergencies.

---

## 3. Repeater Site Monitoring

The fixed stations feature (E70AB at 44.534722, 18.662583 and E74BMN at 44.533694, 18.655361) combined with the device
list and status bar gives operators a **live infrastructure health view**:

- Are repeater stations visible on the map? (Fixed station heartbeats every 30s)
- Which data sources are active? (Status bar shows aprsfi, aprsis, etc.)
- How many devices are being tracked? (Live device count)
- When was the last packet received? (Health endpoint shows last packet age)

---

## 4. DMR-to-APRS Gateway

The DMR-parser module solves a real interoperability gap. DMR (Digital Mobile Radio) is widely used in BiH, but DMR
positions don't appear on APRS networks by default. The system:

1. Decodes DMR audio via DSD+ (connected via audio cable from an HD1 radio)
2. Extracts DMR-ID and GPS coordinates from the digital voice/data stream
3. Looks up callsign via RadioID.net (with 10,000-entry LRU cache)
4. **Forwards to APRS-IS** — making DMR stations visible to the global APRS network
5. Applies 60-second debounce to prevent spam during long transmissions

This bridges two radio ecosystems that otherwise don't communicate.

---

## 5. Real-Time Operations Center Display

The frontend is designed as a **wall-display for an operations room**:

- **Three map layers**: Street (OpenStreetMap), Topographic (OpenTopoMap), Satellite (Esri) — operators choose based on
  the situation
- **Source-coded markers**: Blue (APRS.fi), Purple (APRS-IS), Green (DMR), Orange (Relay), Gray (Fixed) — instant visual
  identification of how each position arrived
- **APRS symbol rendering**: Proper APRS icons (cars, houses, weather stations, etc.) via the hessu/aprs-symbols sprite
  sheet
- **Trail history**: Last 50 unique positions per selected device shown as polylines — track movement paths
- **Auto-center on E70AB**: On load, the map focuses on the primary station
- **Dark mode**: Reduces glare for 24/7 monitoring rooms

---

## 6. Multi-Site Deployment Architecture

The Docker setup (nginx reverse proxy + backend + frontend in one container) and Fly.io config enable several deployment
patterns:

| Deployment         | Where                     | Purpose                                                   |
|--------------------|---------------------------|-----------------------------------------------------------|
| **Command center** | Cloud (Fly.io) or on-prem | Primary dashboard, internet-connected, all sources active |
| **Repeater site**  | Raspberry Pi / mini-PC    | Backend + DMR-parser + Relay sender, runs offline         |
| **Field laptop**   | Portable                  | Frontend-only, connects to command center via VPN/LAN     |
| **Development**    | Local machine             | All modules with mock transport and simulator             |

The relay module bridges the gap between sites that can't share a network.

---

## 7. Technical Appliances Summary

| Use Case                | Modules Used               | Internet | Key Feature                                 |
|-------------------------|----------------------------|----------|---------------------------------------------|
| **Live APRS tracking**  | Backend + Frontend         | Yes      | APRS-IS stream + APRS.fi polling            |
| **Disaster response**   | All four                   | Partial  | LoRa relay store & forward                  |
| **DMR interop**         | DMR-parser + Backend       | Yes      | Audio decode → APRS-IS gateway              |
| **Repeater monitoring** | Backend + Frontend         | Yes      | Fixed stations + status dashboard           |
| **Field exercises**     | Backend + Frontend + Relay | No       | TCP transport over LAN                      |
| **Development/testing** | All (mock mode)            | No       | Simulator + mock transport + link simulator |
| **Off-grid tracking**   | Relay (serial transport)   | No       | LoRa 868MHz point-to-point                  |

---

## 8. What Makes This System Unique

Most APRS tools are **receive-only viewers** (aprs.fi) or **single-source clients** (YAAC, Xastir, APRSdroid). This
system is unique because it:

1. **Aggregates all sources** into one unified view with source attribution
2. **Works without internet** via the LoRa relay — no other web-based APRS tracker does this
3. **Bridges DMR to APRS** — uncommon integration, especially with automatic RadioID lookup
4. **Three-tier storage** (memory → SQLite → Supabase) provides both speed and resilience
5. **Designed for operations**, not just hobbyist viewing — auth, profiles, callsign registration, operations-center UI

This is essentially a **field-deployable Common Operating Picture (COP)** for amateur radio operators supporting
emergency communications — built specifically for the operational reality of BiH's terrain and infrastructure.

---

## 9. Architecture Overview

### Data Flow

```
GPS Sources → Backend Services → In-Memory Store + Local SQLite (+ Supabase backup) → Socket.io Broadcast → Frontend
                                       │
                                       └→ Relay Webhook → Sender Queue → Transport → Receiver → Remote Backend
```

### Module Structure

```
aprs-backend/   # Node.js/TypeScript Express + Socket.io server (port 3001)
aprs-frontend/  # Next.js 14 App Router frontend (port 3000)
lora-relay/     # Offline position sync — store & forward over LoRa/TCP (port 3002)
DMR-parser/     # DMR audio → APRS bridge (DSD+ stdout → POST /api/gps)
```

### Backend Services

- **APRS-IS**: Raw TCP stream parser with compressed + uncompressed format support, auto-reconnect
- **APRS.fi**: HTTP API polling with exponential backoff (ToS-compliant)
- **Store**: Three-layer architecture — in-memory cache (fast reads), SQLite (primary persistence), Supabase (backup)
- **WebSocket**: Full state snapshot on connect, real-time position:update broadcasts
- **REST API**: Device listing, position history, GPS ingest, relay ingest, profile management

### Frontend Capabilities

- **Leaflet map** with Street/Topo/Satellite layers
- **Source-colored markers** and polyline trails
- **Device sidebar** with search, sort, and live time-since updates
- **Supabase Auth** with Google OAuth and email/password
- **Dark/light theme** with localStorage persistence

### Relay System

- **Binary codec**: ~35 bytes/position (microdegree encoding, bitfield flags, length-prefixed strings)
- **Persistent queue**: NDJSON file-backed FIFO, crash-safe
- **Pluggable transports**: Mock (testing), TCP (LAN), Serial (LoRa hardware)
- **Link simulator**: Packet loss, latency, bandwidth throttle, disconnect cycles for stress testing
- **Batch packets**: 6-byte header (magic 0xAE70, version, count, sequence) + concatenated positions

### DMR Parser

- **DSD+ stdin parsing**: Multiple regex patterns for DMR-ID and GPS extraction
- **RadioID.net lookup**: Callsign resolution with 10,000-entry LRU cache
- **APRS-IS forwarding**: Positions sent via /api/gps, forwarded to global APRS network
- **Debouncing**: 60-second per-ID suppression to prevent spam
