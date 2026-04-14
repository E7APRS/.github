# E7APRS Tracker System

Real-time GPS tracking system for amateur radio operators. Ingests position data from multiple sources and streams live updates to a web map.

## Projects

| Folder                                                              | Stack                                      | Port | Purpose                                      |
|---------------------------------------------------------------------|--------------------------------------------|------|----------------------------------------------|
| [aprs-backend/](https://github.com/E7APRS/APRS-dashboard-backend)   | Node.js + TypeScript + Express + Socket.io | 3001 | Data ingestion, storage, WebSocket broadcast |
| [aprs-frontend/](https://github.com/E7APRS/APRS-dashboard-frontend) | Next.js 14 (App Router) + Leaflet          | 3000 | Live map UI                                  |
| [DMR-parser/](https://github.com/E7APRS/dmr-parser)                 | Node.js + TypeScript                       | —    | DMR audio → APRS bridge                      |

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        DATA SOURCES                             │
│                                                                 │
│  APRS.fi API          APRS-IS TCP           DMR Radio (HD1)     │
│  (HTTP polling)       (raw packets)         (audio cable)       │
│       │                    │                      │             │
│       │                    │               DSD+ decoder         │
│       │                    │                      │             │
└───────┼────────────────────┼──────────────────────┼─────────────┘
        │                    │                      │
        └────────────────────┴──────────────────────┘
                             │
                    ┌────────▼────────┐
                    │  aprs-backend   │
                    │                 │
                    │  Express REST   │
                    │  Socket.io WS   │
                    │                 │
                    │  In-memory      │
                    │  cache          │
                    │       +         │
                    │  Supabase DB    │
                    └────────┬────────┘
                             │  WebSocket
                             │  (positions:snapshot)
                             │  (history:snapshot)
                             │  (position:update)
                    ┌────────▼────────┐
                    │  aprs-frontend  │
                    │                 │
                    │  Leaflet map    │
                    │  Device list    │
                    │  Trail history  │
                    └─────────────────┘
```

---

## Data Sources

### APRS.fi (`aprsfi`)
- Polls the APRS.fi HTTP API at a configurable interval
- Requires API key (free registration at aprs.fi)
- Configured callsign list in `.env`

### APRS-IS (`aprsis`)
- Direct TCP connection to APRS-IS Tier 2 network
- Requires valid amateur radio callsign + passcode
- Server-side packet filter (e.g. `p/E7` = all E7 prefix callsigns)
- Parses both uncompressed and compressed APRS position formats
- Extracts symbol table + symbol code from every packet

### DMR (`dmr`)
- HD1 radio connected via audio cable to PC line-in
- DSD+ software decodes DMR digital audio and extracts DMR-ID + GPS
- DMR-parser bridges DSD+ output to the backend via `POST /api/gps`
- Only posts when GPS coordinates are present in the DMR transmission
- Callsign resolved via RadioID.net database

### Fixed Stations
- Statically configured in `aprs-backend/src/services/fixed-stations.ts`
- Always active, broadcast every 30s
- Used for known fixed infrastructure (repeaters, club stations)

---

## Database (Supabase)

```sql
CREATE TABLE devices (
  radio_id     TEXT PRIMARY KEY,
  callsign     TEXT NOT NULL,
  last_seen    TIMESTAMPTZ NOT NULL,
  last_lat     FLOAT8,
  last_lon     FLOAT8,
  source       TEXT
);

CREATE TABLE positions (
  id           BIGSERIAL PRIMARY KEY,
  radio_id     TEXT,
  callsign     TEXT,
  lat          FLOAT8,
  lon          FLOAT8,
  altitude     FLOAT8,
  speed        FLOAT8,
  course       FLOAT8,
  comment      TEXT,
  symbol       TEXT,
  symbol_table TEXT,
  source       TEXT,
  timestamp    TIMESTAMPTZ,
  created_at   TIMESTAMPTZ DEFAULT now()
);
```
## Position History Trimming Trigger
```sql
CREATE OR REPLACE FUNCTION trim_position_history()
RETURNS TRIGGER AS $$
BEGIN
  DELETE FROM positions
  WHERE radio_id = NEW.radio_id
    AND id NOT IN (
      SELECT id FROM positions
      WHERE radio_id = NEW.radio_id
      ORDER BY timestamp DESC
      LIMIT 10
    );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_trim_position_history
AFTER INSERT ON positions
FOR EACH ROW EXECUTE FUNCTION trim_position_history();
```

Trigger `trg_trim_position_history` keeps only the last 10 positions per `radio_id` in the database. The backend in-memory cache holds up to 100.

---

## APRS Symbols

Icons use the [hessu/aprs-symbols](https://github.com/hessu/aprs-symbols) sprite sheets served via jsDelivr CDN.

Each symbol is identified by two characters:
- **Symbol table**: `/` = primary, `\` = alternate
- **Symbol code**: ASCII character (e.g. `-` = house, `[` = person, `y` = service)

Sprite position formula: `index = charCode(code) - 33`, grid of 16 columns × 24px per tile.

---

## Quick Start

```bash
# 1. Backend
cd aprs-backend
cp .env.example .env   # fill in credentials
npm install
npm run dev            # http://localhost:3001

# 2. Frontend
cd aprs-frontend
cp .env.local.example .env.local
npm install
npm run dev            # http://localhost:3000

# 3. DMR bridge (optional)
cd DMR-parser
cp .env.example .env
npm install
.\DSDPlus.exe -i 0 | npm run dev
```

---

## REST API

| Method | Endpoint                          | Description                      |
|--------|-----------------------------------|----------------------------------|
| GET    | `/api/status`                     | Active sources, config           |
| GET    | `/api/devices`                    | All known devices                |
| GET    | `/api/devices/:radioId`           | Single device                    |
| GET    | `/api/positions/latest`           | Latest position per device       |
| GET    | `/api/positions/:radioId/history` | Full history from DB             |
| POST   | `/api/gps`                        | Manual position push (DMR, etc.) |

## WebSocket Events

| Event                | Direction       | Payload                                |
|----------------------|-----------------|----------------------------------------|
| `positions:snapshot` | server → client | `Position[]` — all current positions   |
| `history:snapshot`   | server → client | `Record<radioId, Position[]>` — trails |
| `position:update`    | server → client | `Position` — single new position       |

## Contributions 

If you find this project interesting and would like to contribute, you're very welcome - whether you're a developer, radio amateur, or just curious about APRS/DMR systems.

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

Copyright (c) 2026 Adin Bešlagić. All rights reserved.  
Reuse permitted only with written permission from the author.  
See [LICENSE](https://github.com/E7APRS/.github/tree/master?tab=License-1-ov-file) for full terms.
