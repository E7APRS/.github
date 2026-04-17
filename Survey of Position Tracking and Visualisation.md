Research of Position Tracking and Visualization Systems
===================================================================

APRS, LoRa, DMR, Disaster Response, and Multi-Source Aggregation Architectures

* * * * *

Table of Contents
-----------------

1. [Executive Summary](#1-executive-summary)
2. [Web-Based APRS Trackers](#2-web-based-aprs-trackers)
3. [Desktop APRS Clients](#3-desktop-aprs-clients)
4. [Mobile APRS Applications](#4-mobile-aprs-applications)
5. [Self-Hosted APRS Servers (Infrastructure)](#5-self-hosted-aprs-servers-infrastructure)
6. [Open-Source APRS Web Dashboards](#6-open-source-aprs-web-dashboards)
7. [Software Modems and TNC Software](#7-software-modems-and-tnc-software)
8. [LoRa APRS Ecosystem](#8-lora-aprs-ecosystem)
9. [Meshtastic and Mesh Networking](#9-meshtastic-and-mesh-networking)
10. [DMR-to-APRS Bridges](#10-dmr-to-aprs-bridges)
11. [Meshtastic-to-APRS Bridges](#11-meshtastic-to-aprs-bridges)
12. [Disaster Response Tracking Systems](#12-disaster-response-tracking-systems)
13. [Multi-Source Aggregation and Real-Time Architectures](#13-multi-source-aggregation-and-real-time-architectures)
14. [Comparison Matrix](#14-comparison-matrix)
15. [Key Differentiators of E7APRS](#15-key-differentiators-of-e7aprs)
16. [Sources](#16-sources)

* * * * *

1\. Executive Summary
---------------------

This document provides a comprehensive survey of existing position tracking, visualization, and relay systems relevant
to amateur radio, disaster response, and civil protection. It covers web-based and desktop APRS trackers, mobile
applications, self-hosted infrastructure, LoRa APRS projects, Meshtastic mesh networking, DMR integration bridges,
professional disaster response tools, and real-time multi-source aggregation architectures.

Each system is evaluated against a consistent set of criteria: multi-source aggregation capability, real-time streaming
method, offline operation, backend/storage architecture, open-source status, DMR integration, and LoRa relay support.

The survey reveals that while individual systems excel in specific domains, no existing open-source project combines
multi-source aggregation (APRS-IS, APRS.fi API, DMR, LoRa relay, simulator), persistent store-and-forward over LoRa,
three-tier storage (in-memory, SQLite, cloud backup), and a modern web-based map interface in a single self-hosted
platform.

* * * * *

2\. Web-Based APRS Trackers
---------------------------

### 2.1 aprs.fi

- URL: <https://aprs.fi/>
- Developer: Heikki Hannikainen, OH7LZB (GainIO)
- License: Closed source (web); iOS app is paid ($6.99)
- Data sources: APRS-IS full feed, CWOP, AIS
- Real-time updates: Yes, live map with instant station display
- Offline: No (web version). iOS app can connect to a BLE TNC and operate without cellular, beaconing position and
  receiving APRS packets directly over radio
- Backend/database: Yes, full backend with historical data, telemetry graphs, statistics, and weather data. Archives
  position history for months
- Multi-source: Yes (APRS-IS, CWOP, AIS)
- DMR integration: No native DMR support (but DMR-to-APRS-IS gateways like BrandMeister feed data that shows up here)
- LoRa relay: No native support (but LoRa iGates that forward to APRS-IS appear on the map)
- Key features: Search-as-you-type, multi-station tracking, weather/telemetry/statistic graphs, map filtering (weather
  stations, AIS, custom), station coverage info, Dark Mode, Retina graphics. Subscription adds APRS messaging, software
  DSP modem, APRS-IS beaconing with 10 callsign profiles, RX iGate, long time ranges
- Latest update: v2.8.1 (April 2026) --- added iGate/digipeater status, "Stations heard" tables, raw packet decoding

### 2.2 APRS Direct / APRS Track Direct

- URL: <https://www.aprsdirect.com/> / <https://aprs-map.info/>
- Developer: qvarforth
- License: Open source --- <https://github.com/qvarforth/trackdirect>
- Data sources: APRS-IS, CWOP-IS, OGN (Open Glider Network)
- Real-time updates: Yes, via WebSocket backend
- Offline: No
- Backend/database: Yes, full PostgreSQL backend with Python data collectors, WebSocket server, and Apache/nginx
  frontend. Docker Compose deployment available
- Multi-source: Yes (APRS-IS, CWOP-IS, OGN, or any APRS data source)
- DMR integration: No
- LoRa relay: No native support
- Key features: Time travel (view map at historical date/time), heatmap generation, "Latest Heard" and "Station Search"
  functions, configurable time range for displayed data. Leaflet or Google Maps rendering. Self-hostable with Docker
- Note: This is the most significant self-hosted open-source APRS web platform. Powers aprsdirect.com, aprs-map.info,
  and gliderradar.com

### 2.3 aprs.to (N2RWE Live Real-Time APRS Map)

- URL: <https://aprs.to/>
- Developer: Steve White, N2RWE
- License: Closed source
- Data sources: APRS-IS, CWOP
- Real-time updates: Yes, live WebSocket-based updates with connection recovery
- Offline: No
- Backend/database: Yes, full backend with user accounts
- Multi-source: Yes (APRS, CWOP)
- DMR integration: No
- LoRa relay: No
- Key features: 6 map overlays (hiking, cycling, ski, railroad, sea), "Map Snapshots" (capture and recall map state at
  any point in time), "Track My Location" (browser geolocation), station coverage maps (heatmap + convex hull polygon),
  live packet feed, most recent beacons (up to 500), fully searchable/sortable/customizable data tables, station view
  counter, location search, Google My Maps integration. User accounts with saved preferences

### 2.4 findu.com

- URL: <https://www.findu.com/>
- Developer: Steve Dimse, K4HG
- License: Closed source
- Data sources: APRS-IS, CWOP
- Real-time updates: Near-real-time (~20 packets/second ingestion), but web pages are dynamically generated on request
  rather than push-based
- Offline: No
- Backend/database: Yes, massive MySQL database (400+ GB), Perl CGIs, ~1.5 million dynamic pages/day. Dell 12-core Xeon
  server with live backup
- Multi-source: Yes (APRS-IS, CWOP)
- DMR integration: No
- LoRa relay: No
- Key features: One of the oldest APRS web services. Historical data archive, weather data forwarding to NOAA (100,000+
  observations daily), ISS APRS relay archive, RSS feed output for APRS messages, URL-based query API, balloon flight
  tracking with full log retrieval (up to 180 days), tabular data export

### 2.5 aprs.no (Norway)

- URL: <https://aprs.no/> / <https://kart.aprs.no/>
- Developer: Norwegian amateur radio community, associated with Norsk Radio Relæ Liga (NRRL)
- License: Closed source
- Data sources: APRS-IS (Norwegian focus)
- Real-time updates: Yes
- Offline: No
- Backend/database: Yes
- Key features: Norway-focused APRS map service. Limited public documentation

### 2.6 aprsdirect.de

- URL: <https://aprsdirect.de/>
- License: Closed source, based on Track Direct tools
- Data sources: APRS-IS, LoRa APRS
- Real-time updates: Yes
- Key features: Dual map --- both APRS and LoRa live maps, Google Maps-based, historical time selection, heatmap
- Note: Discontinued as of March 31, 2026

### 2.7 lora-aprs.live

- URL: <https://lora-aprs.live/>
- Data sources: Direct iGate syslog data (NOT APRS-IS) --- LoRa iGates running CA2RXU firmware
- Key features: iGate coverage maps, multi-tracker views, RF performance monitoring (SNR, RSSI, packet logs), MQTT
  message viewer, per-iGate statistics. Specialized analytics platform rather than a general APRS tracker
- LoRa relay: Yes, this is LoRa-native

### 2.8 GliderRadar

- URL: <https://www.gliderradar.com/>
- License: Based on Track Direct / open source tools
- Data sources: OGN (Open Glider Network), APRS-IS, CWOP-IS
- Key features: Specialized for aviation/glider tracking using FLARM data via OGN, which uses APRS protocol

* * * * *

3\. Desktop APRS Clients
------------------------

### 3.1 YAAC (Yet Another APRS Client)

- URL: <https://www.ka2ddo.org/ka2ddo/YAAC.html> / <https://sourceforge.net/projects/yetanotheraprsc/>
- Developer: Andrew Pavlin, KA2DDO
- License: Open source (LGPL)
- Platform: Java --- Windows, Linux, Raspberry Pi (AWT/Swing GUI)
- Multi-source: Yes (RF via TNC + APRS-IS internet + GPS)
- Real-time: Yes
- Offline: Yes --- fully functional for emergency operations disconnected from internet. OpenStreetMap tiles can be
  pre-downloaded for offline mapping
- Backend/database: Local data store
- DMR integration: No
- LoRa relay: No
- Key features: 16 data reporting views, I-Gate, digipeater, OpenTRAC protocol support, SSL/TLS APRS-IS connections,
  extensible via plugins (TAK/ATAK integration, sound alerts, telemetry alarms, GPX dynamic objects). Supports
  OpenStreetMap and terrain elevation maps

### 3.2 Xastir

- URL: <https://github.com/Xastir/Xastir> / <http://xastir.org/>
- Developer: The Xastir Group (originally Frank Giannandrea, KC2GJS)
- License: Open source (GPL)
- Platform: X11 --- Linux, FreeBSD, Mac OS X, Solaris, Windows (via compilation)
- Multi-source: Yes (RF via serial KISS TNC, APRS-IS, GPS)
- Real-time: Yes
- Offline: Yes --- offline maps supported, RF-only operation without internet
- Backend/database: Local
- DMR integration: No
- LoRa relay: No
- Key features: 125 map formats, 7 languages, two-way messaging, object display, NWS weather alert gating from internet
  to RF, mobile mapping with GPS, TCP and UDP packet injection ports. Arguably the most capable open-source APRS
  implementation. Active since 2000

### 3.3 APRSISCE/32

- URL: <http://aprsisce.wikidot.com/>
- Developer: Lynn Deffenbaugh, KJ4ERJ
- License: Closed source (freeware)
- Platform: Windows, Windows Mobile/CE (runs on Linux/Mac via WINE)
- Multi-source: Yes (APRS-IS + RF via TNC + GPS)
- Real-time: Yes
- Offline: Partial --- RF operation works without internet; map tiles are cached from OpenStreetMap
- Backend/database: Local
- DMR integration: No
- LoRa relay: No
- Key features: Dynamic server-side filtering (auto-adjusts APRS-IS filter as you pan the map), unique MultiTrack
  feature (separate tracking windows per station, dual-monitor support), OpenStreetMap tiles with adjustable
  transparency, enhanced messaging, ANSRVR announcement support, configurable digipeater, GPS logging for OSM. Described
  as "the most fully featured Windows APRS software available"

### 3.4 PinPoint APRS

- URL: <https://www.pinpointaprs.com/>
- Developer: Frank Watervoort, AB0WV
- License: Closed source (free for non-commercial use)
- Platform: Windows 7/8/10/11
- Multi-source: Yes (APRS-IS + RF via TNC/Direwolf/Soundmodem/VARA + GPS)
- Real-time: Yes
- Offline: Yes --- caches map tiles for offline use, supports RF-only operation in rural/disconnected areas
- Backend/database: Local
- DMR integration: No
- LoRa relay: No
- Key features: Built-in mapping (Google, Bing, OpenStreetMap, satellite/hybrid), RX-only iGate, tactical callsigns, GPX
  import, APRS messaging/SMS/email, up to 1000 tracking points. Featured in ARRL QST magazine. Battle-tested by ARES
  volunteers. Actively updated (February 2026 update added serial flow control, bug fixes, updated APRS symbols)

### 3.5 UI-View32

- URL: <https://www.ui-view.net/>
- Developer: Roger Barker, G4IDE (SK 2004)
- License: Closed source (now free registration)
- Platform: Windows 95/98/ME/2000/XP/Vista/7/8/10
- Multi-source: Yes (APRS-IS + RF via TNC)
- Real-time: Yes
- Offline: Partial --- RF operation works offline
- Backend/database: Local
- DMR integration: No
- LoRa relay: No
- Key features: Built-in intelligent digipeater, full APRS-IS server connectivity, customizable display formats, open
  extensible architecture with rich add-on ecosystem (NWS weather, email gateway, RDF plotting). Has its own efficiency
  protocol for fixed station communication
- Status: No longer actively developed since the author's passing in 2004, but still widely used

### 3.6 SARTrack

- URL: <https://www.sartrack.co.nz/>
- Developer: SARTrack Limited (New Zealand)
- License: Free (not open source)
- Platform: Windows
- Multi-source: Yes --- APRS (TNC + APRS-IS), commercial GPS trackers (Kenwood FleetSync, Icom, Tait, Motorola, Hytera),
  AIS vessel tracking, satellite trackers, hand-held GPS import
- Real-time: Yes
- Offline: Yes --- maps cache for offline use, RF-only operation, multi-PC networking over LAN
- Backend/database: Yes --- local database server with internet master server mirroring, encrypted data transport
- DMR integration: No direct DMR, but supports Hytera and Motorola commercial radio GPS tracking
- LoRa relay: No
- Key features: Purpose-built for Search and Rescue. Operations log with automatic event logging, radio operator screen,
  statistical ring system (ISRID-based), search area drawing, multi-PC sync over LAN, encrypted messaging, timeline
  view, multi-monitor support. Two master servers (NZ and Germany)

* * * * *

4\. Mobile APRS Applications
----------------------------

### 4.1 APRSdroid (Android)

- URL: <https://aprsdroid.org/> / <https://github.com/ge0rg/aprsdroid>
- Developer: Georg Lukas
- License: Open source (GPLv2, Scala)
- Multi-source: APRS-IS (TCP), AFSK (audio to/from radio), Bluetooth TNC, UDP, HTTP
- Real-time: Yes
- Offline: Partial --- can work RF-only via audio/Bluetooth TNC without internet. Map requires cached tiles
- Backend/database: Local on-device
- DMR integration: No
- LoRa relay: Can connect to LoRa APRS trackers via Bluetooth TNC
- Key features: Position reporting (one-time or periodic), Smart Beaconing, APRS messaging, station tracking on map,
  AFSK decoding via microphone, status bar notifications. Updated January 2026 for Android 14/15
- Notable fork: NA7Q APRSdroid --- adds professional-grade features, removes Google Maps dependency, uses offline
  MBTiles/MapsForge mapping

### 4.2 APRS.fi iOS App

- URL: <https://apps.apple.com/us/app/aprs-fi/id922155038>
- Developer: GainIO (Heikki Hannikainen, OH7LZB)
- License: Closed source ($6.99)
- Multi-source: APRS-IS, BLE TNC (Mobilinkd TNC3), built-in software DSP modem (audio cable to radio)
- Real-time: Yes
- Offline: Yes --- via BLE TNC connection, operates without cellular data
- Backend/database: Server-side (aprs.fi backend)
- DMR integration: No
- LoRa relay: Can pair with LoRa APRS trackers via BLE TNC
- Key features: Real-time position tracking, APRS messaging, telemetry display, customizable filtering, software modem (
  Extra subscription), APRS-IS beaconing with 10 profiles (Extra subscription), RX-only iGate (Extra subscription),
  heard stations list. Described as a "game-changer for field operations" by SAR users

### 4.3 APRS Pro Deluxe (iOS)

- URL: <https://apps.apple.com/us/app/aprs-pro-deluxe/id1163741288>
- License: Closed source (RX free, TX paid)
- Key features: Built-in 300/1200 baud TNC audiomodem for direct radio connection without cellular, simultaneous RF +
  APRS-IS, built-in digipeater, search for repeaters/EchoLink nodes/ham events, chat messaging with server-side message
  retrieval for offline delivery

### 4.4 APRSKit (iOS)

- URL: <https://apps.apple.com/us/app/aprskit-live-aprs-tracker/id6744252867>
- Key features: Lightweight modern iOS app, live GPS location sharing, direct APRS-IS integration, nearby station map,
  customizable SSID/icon/comment, adjustable update intervals, Apple Maps integration

### 4.5 PulseModem A (iOS)

- URL: <https://www.pulsemodem.com/>
- License: Open source
- Key features: Built-in signal scope for RF visualization, APRS modem

* * * * *

5\. Self-Hosted APRS Servers (Infrastructure)
---------------------------------------------

### 5.1 aprsc

- URL: <http://he.fi/aprsc/> / <https://github.com/hessu/aprsc>
- Developer: Matti Aarnio (OH2MQK) and Heikki Hannikainen (OH7LZB)
- License: Open source (BSD license)
- Language: C (~11,000 lines)
- Purpose: Core APRS-IS server for Tier 1/Tier 2 network backbone. NOT a tracking frontend --- it relays raw APRS
  packets between servers and clients
- Key features: Fullfeed ports, filtered iGate ports, UDP submit, HTTP status page, HTTP upload, duplicate filtering.
  Intentionally minimal --- no iGating, digipeating, radio interfacing, or object generation
- Platform: Linux, FreeBSD, Solaris, Mac OS X. Packaged for Debian/Ubuntu/CentOS
- Note: Used on the majority of APRS-IS core servers worldwide. Stable, feature-complete, rarely needs updates

### 5.2 javAPRSSrvr

- URL: <http://www.aprs-is.net/javaprssrvr/>
- Developer: Pete Loveall, AE5PL
- License: Closed source (Java)
- Purpose: The de facto standard APRS-IS server since 2002. More feature-rich than aprsc
- Key features: Flexible client ports (TCP + UDP + HTTP/HTTPS + WebSocket), HTTP 1.1 authentication, server-side
  filtering (APRSFilter with 13 filter types), built-in gateway clients (APRSIGate, WhoIsGate, EmailGate, CQGate),
  D-PRS (D-STAR) interface on port 14551, WXNowGen weather client, telemetry support, RESTful status pages
- Note: More of a full-featured APRS-IS server platform than aprsc, with built-in adjunct services

### 5.3 aprx

- URL: <https://github.com/PhirePhly/aprx> / <https://thelifeofkenneth.com/aprx/>
- Developer: Matti Aarnio (OH2MQK), maintained by Kenneth Finnegan
- License: Open source
- Purpose: Lightweight APRS digipeater and iGate daemon for Linux/POSIX
- Key features: Multi-port digipeater, bidirectional iGate, D-PRS to APRS conversion, minimal dependencies (only libc),
  tiny memory footprint (~250 KB), runs on embedded systems (OpenWrt, NSLU2), built-in Erlang monitor for telemetry,
  smart packet filtering, supports KISS and "TNC2 monitor" TNCs
- Note: Ideal for dedicated headless iGate/digipeater on a Raspberry Pi or embedded device

* * * * *

6\. Open-Source APRS Web Dashboards
-----------------------------------

### 6.1 APRS Track Direct (trackdirect)

- URL: <https://github.com/qvarforth/trackdirect>
- Language: Python 2 (backend), JavaScript (frontend with Leaflet or Google Maps)
- Database: PostgreSQL
- Architecture: aprsc collector → Python data processors → PostgreSQL → WebSocket server → browser
- Docker: Yes (docker-compose with aprsc, collector, websocket, cron services)
- Key features: The most complete self-hosted APRS web platform. Heatmap generation, time travel, station search, "
  latest heard". Powers aprsdirect.com and aprs-map.info
- Status: Active (82+ stars, 44+ forks). Plans to rewrite backend in Python 3 with Angular frontend and REST API

### 6.2 APRSWeb

- URL: <https://github.com/ccmolik/aprsweb>
- Description: Self-hosted aprs.fi alternative. Connects to Direwolf (or AGWPE server), serializes APRS frames to
  JSON/protobuf via Twirp, renders on web map
- Key features: Runs on Raspberry Pi, doubles as HTTP API for APRS frames, Kubernetes deployment example included
- Note: More of a local RF receiver viewer than a full APRS-IS frontend

### 6.3 local-aprs

- URL: <https://github.com/trasukg/local-aprs>
- Description: Local web server for APRS with WebSocket real-time updates. Angular client served by Node.js backend
- Key features: Live packet forwarding via WebSocket, local "slippy map" tile serving for offline areas, APRS message
  sending from browser, shares radio hardware over local WiFi
- Note: Designed for field deployment where multiple operators share one radio/TNC

### 6.4 Direwolf APRS Web Dashboard

- URL: <https://github.com/PC7MM/Direwolf-APRS-Web-Dashboard>
- Description: Feature-rich web dashboard specifically for Direwolf
- Key features: Per-station tables with static/moving indicators, map of APRS users and iGates, Direwolf console output,
  APRS + AIS display, daily logfile browsing

### 6.5 APRS Frontend

- URL: <https://github.com/japalie/aprs-frontent>
- Description: APRS Web GUI with Apache, MariaDB, PHP backend
- Key features: Configurable max age/objects, automatic database cleanup

### 6.6 APRStac

- URL: <https://www.aprstac.com/>
- License: Open source, single binary
- Platform: Windows, Linux, Raspberry Pi
- Key features: All-in-one: real-time Leaflet map, digipeater (WIDEn-N), bidirectional iGate, BBS (bulletin board over
  RF), email gateway (SMTP/IMAP), Meshtastic bridge, heading arrows, Maidenhead grid overlay, MGRS coordinates, station
  ghosting. Web-based interface accessible from any device on LAN
- Note: Perhaps the closest existing project to what E7APRS does --- modern web UI, multiple data paths, self-hosted.
  But focused on RF/iGate rather than multi-source aggregation with database persistence

### 6.7 nyuad-aprs-server

- URL: <https://github.com/ha5dzs/nyuad-aprs-server>
- Description: Simple APRS map based on trackdirect, modified for specific requirements. Python + PostgreSQL, listens on
  APRS and CWOP networks

* * * * *

7\. Software Modems and TNC Software
------------------------------------

### 7.1 Dire Wolf

- URL: <https://github.com/wb2osz/direwolf>
- License: Open source (GPLv2)
- Key features: Software soundcard AX.25 packet modem/TNC and APRS encoder/decoder. Standalone tracker, digipeater,
  APRStt gateway, iGate. FX.25 and IL2P forward error correction. 300/1200/9600 baud. Up to 3 soundcards and 6 radios.
  Compatible with SDRs. AIS and EAS decoding. AGWPE-compatible interface (port 8000) for connecting to APRS clients
- Note: The de facto standard software TNC. Many other projects (APRSWeb, Direwolf Dashboard, PinPoint, etc.) connect to
  Dire Wolf as their radio interface

### 7.2 OpenWebRX / OpenWebRX+

- URL: <https://github.com/jketterl/openwebrx> / <https://fms.komkon.org/OWRX/>
- License: Open source (AGPL v3)
- Key features: Multi-user SDR receiver with web interface. Uses Direwolf-based APRS packet demodulation. Decoded APRS
  stations appear on built-in map. APRS-IS iGate reporting. Background APRS decoding. OpenWebRX+ fork adds DMR, D-STAR,
  YSF, NXDN decoding plus improved maps
- Note: Not primarily an APRS tracker, but provides web-based APRS decoding + mapping as part of a broader SDR platform.
  The OpenWebRX+ fork notably decodes DMR alongside APRS

* * * * *

8\. LoRa APRS Ecosystem
-----------------------

### 8.1 OE5BPA / Peter Buchegger --- LoRa_APRS_Tracker (The Original)

Peter Buchegger (OE5BPA) created the foundational LoRa APRS tracker and iGate firmware for ESP32 boards, establishing
the de facto standard for LoRa APRS operation worldwide.

- Repos: <https://github.com/lora-aprs/LoRa_APRS_Tracker>, <https://github.com/lora-aprs/LoRa_APRS_iGate>
- License: Open source (GPL)
- Hardware: TTGO T-Beam V0.7/V1.0/V1.1/V1.2, Heltec WiFi LoRa 32 V1/V2, TTGO LoRa32 V1/V2/V2.1
- Packet format: Standard APRS text-based packets over LoRa --- relatively verbose (~113 bytes for a position report
  including full AX.25-style headers)
- Store-and-forward: No
- APRS-IS integration: Via iGate firmware (receives LoRa, forwards to APRS-IS over WiFi)
- Status: The iGate repo was archived September 2025. Peter now recommends CA2RXU's firmware as the successor

### 8.2 CA2RXU / Ricardo Guzman --- LoRa_APRS_Tracker & LoRa_APRS_iGate (Current Standard)

The most actively developed LoRa APRS firmware ecosystem.

Tracker: <https://github.com/richonguzman/LoRa_APRS_Tracker>

- Stars: ~464, Latest release: V2.4.3.1 (April 2026)
- License: GPL-3.0
- Features: Tx+Rx, messaging (read/write/delete with I2C keyboard or phone), weather station (BME280/BME680/BMP280),
  Winlink email via APRSLink, Bluetooth TNC (BLE + Classic, KISS/TNC2 for APRSDroid/aprs.fi iOS), digipeater mode (
  emergency menu), smart beaconing with turn-slope, power management (240MHz down to 80MHz saves ~20%), OLED eco mode
- Position encoding: All GPS beacons use Base91 compressed APRS format to minimize RF transmission time (13 bytes for
  lat/lon/symbol/course/speed vs ~40+ bytes uncompressed)
- Store-and-forward: In-memory message buffer (500+ messages) for received packets, but no persistent queue for position
  relay
- Supported boards: T-Beam V1.x, T-Beam S3 Supreme V3, Heltec V3/V3.2, T-Deck Plus, LightTracker Plus 1.0, DIY
  ESP32+SX1262/SX1268/LLCC68/E220 1W, and many more
- 3 switchable frequencies: EU 433.775, PL 438.xxx, UK 439.9125

iGate: <https://github.com/richonguzman/LoRa_APRS_iGate>

- Stars: ~466, Latest release: V3.2.3 (March 2026)
- Features: Bidirectional LoRa-to-APRS-IS gateway, digipeater with EcoMode (board sleeps until packet Rx, ~24mA idle
  current vs 150mA normal), cross-frequency digipeating, WIDE1-1/WIDE2-n path support, MIC-E decoding, backup digipeater
  mode when WiFi drops

### 8.3 sh123 --- esp32_loraprs (KISS Modem + iGate + DV)

- Repo: <https://github.com/sh123/esp32_loraprs>
- Stars: ~262
- License: GPL-3.0
- Key differentiator: True KISS TNC interface, making it interoperable with desktop APRS software (APRSIS32, APRSDroid,
  direwolf). Supports both AX.25 and TNC2 text packet modes. Also includes Codec2 DV modem (walkie-talkie mode)
- Store-and-forward: No persistent queue

### 8.4 nakhonthai --- ESP32APRS_LoRa (All-in-One)

- Repo: <https://github.com/nakhonthai/ESP32APRS_LoRa>
- License: Open source
- Features: iGate + Digipeater + Tracker + Weather Station + Telemetry in a single firmware. Bluetooth Master SPP/BLE
  TNC, PPPoS GSM modem support, WireGuard VPN, GFSK9600 (G3RUH) modem, AIS receiver iGate, Mic-E compression, web
  configuration UI

### 8.5 LightAPRS / LightTracker (Balloon & Ultra-Lightweight Trackers)

- Organization: <https://github.com/lightaprs>
- License: Open source
- Key products:
    - LightTracker 1.1 433MHz: One of the smallest/lightest LoRa APRS trackers. ARM Cortex-M0 (ATSAMD21G18) + SX1262,
      5.21 grams, for pico balloons/HAB/drones
    - LightTracker Plus 1.0: ESP32-based, 1W LoRa (30 dBm), GPS with balloon mode up to 80km altitude
    - LightLoRaAPRS: Single firmware with Tracker + iGate + Digi modes, switchable via WiFi web config
- Store-and-forward: No

### 8.6 APRS 434 / APRS 438 (ON4AA --- Maximum Compression)

- Website: <https://aprs434.github.io/> and <https://aprs438.readthedocs.io/>
- Repos: <https://github.com/aprs434/lora.tracker>, <https://github.com/aprs434/lora.igate>
- Author: Serge Y. Stroobandt, ON4AA
- License: Open source
- Key innovation: Aggressive binary frame compression for LoRa APRS. CCCCD header (5 bytes vs 26 bytes OE5BPA) + Base91
  compressed position body (13 bytes) = ~18--30 bytes total per position (max 45 bytes by design). The iGate
  decompresses and reconstructs full APRS-IS packets for forwarding
- Store-and-forward: No

### 8.7 Binary Codec Comparison

|

Codec

|

Position Size

|

Callsign Included?

|

Timestamp?

|

Batch Support?

|
| --- | --- | --- | --- | --- |
| OE5BPA legacy | ~113 bytes | Yes (text) | Yes | No |
| APRS compressed (Base91) | ~30--50 bytes | Yes (text header) | Optional | No |
| APRS 438 (ON4AA) | ~18--30 bytes | Yes (4B compressed) | No | No |
| E7APRS binary | ~32--38 bytes | Yes (length-prefixed) | Yes (4B epoch) | Yes (6B header) |
| Meshtastic protobuf | ~28--50 bytes + 16B header | Node ID only | Optional | No |

E7APRS's codec is competitive with APRS 438 in raw size while carrying more metadata (timestamp, source type,
variable-length identifiers). The batch framing (magic `0xAE70` + version + count + sequence) is unique and enables
multi-position packets over a single LoRa transmission --- fitting ~3 positions in a typical LoRa packet, which no other
LoRa APRS project does.

### 8.8 Hardware Platform Compatibility

|

Firmware

|

T-Beam

|

T3/LoRa32

|

Heltec V3

|

T-Deck

|

LightTracker

|
| --- | --- | --- | --- | --- | --- |
| CA2RXU Tracker | Yes | Yes (BT TNC) | Yes | Yes | Yes |
| CA2RXU iGate | Yes | Yes | Yes | Yes | --- |
| ESP32APRS_LoRa | Yes | Yes | Yes | --- | --- |
| esp32_loraprs | Yes (SX127X) | DIY | --- | --- | --- |
| Meshtastic | Yes | Yes | Yes | Yes | Yes |
| MeshCore | Yes | --- | Yes | Yes | --- |
| LightLoRaAPRS | --- | --- | --- | --- | Yes |
| APRS 434/438 | Yes | --- | --- | --- | --- |

Recommended hardware for new builds:

- Trackers: T-Beam S3 Supreme V3 (ESP32-S3 + 8MB PSRAM + SX1262 + GPS, ~$30)
- iGates/Digipeaters: T3S3 V1.2 or Heltec V3 (compact, low-power, 24mA EcoMode)
- Messaging: T-Deck Plus (built-in keyboard + screen)

* * * * *

9\. Meshtastic and Mesh Networking
----------------------------------

### 9.1 Meshtastic

- Website: <https://meshtastic.org/>
- Repo: <https://github.com/meshtastic>
- License: Open source (GPL-3.0)
- Band: ISM (license-free) --- 915 MHz (US), 868 MHz (EU), 433 MHz
- Encryption: End-to-end AES256 on private channels (prohibited on amateur bands)

How it works: Managed flooding protocol --- every node can relay messages. No central controller. Nearby nodes
rebroadcast selectively until destination is reached or message expires. Simpler but generates more RF traffic than
directed routing.

Position sharing: Built-in. Position messages use Protocol Buffers encoding. The Position message contains 23 fields
including `latitude_i` (sfixed32, scaled by 1e-7), `longitude_i` (sfixed32), `altitude` (int32 meters MSL),
`ground_speed`, `ground_track`, `time`, GPS accuracy metrics. Minimal position payload (lat+lon+alt) is approximately
12--16 bytes of protobuf, plus a 16-byte raw packet header. Maximum data payload is 233 bytes.

Store-and-forward: Limited:

- Only ESP32 devices with onboard PSRAM (T-Beam, T3S3) can act as S&F servers
- Stores text messages only --- NOT position data
- Storage: ~11,000 records using 2/3 of PSRAM (volatile --- lost on reboot)
- No persistent flash storage for S&F
- Retention default: 240 minutes, max return: 25 messages
- Only works on private channels, not public LongFast
- Since firmware 2.4, automatic history retrieval on reconnect

Comparison to dedicated APRS relay: Meshtastic excels at mesh messaging locally but has no equivalent to E7APRS's
persistent NDJSON queue, no crash-safe store-and-forward for positions, and no APRS-IS integration (uses MQTT gateways
instead). Lacks the transport abstraction layer (mock/TCP/serial swappable transports).

### 9.2 MeshCore

- Website: <https://meshcore.co.uk/>, [https://meshcore.io](https://meshcore.io/)
- Repo: <https://github.com/meshcore-dev/MeshCore>
- Stars: ~2,500
- License: MIT license
- Inspired by: Cyclone Gabrielle NZ response

Key difference from Meshtastic: Hybrid routing instead of flooding. On first contact, MeshCore floods to discover the
path. Once delivered, the return acknowledgment records the route. Subsequent messages are directed along learned paths,
dramatically reducing RF congestion.

Device roles:

1. Companion Radio: Personal device (BLE/USB/WiFi to phone), does NOT relay others' traffic
2. Repeater: Relay-only, no user traffic generation. Place high for coverage
3. Room Server: BBS-style message board with persistent message history. Users reconnect later to retrieve missed
   messages (true store-and-forward for group communications)

Store-and-forward: Room Servers provide persistent message storage for group communications. Roaming users can retrieve
missed history. The network is self-healing with no central server required.

### 9.3 APRS-over-LoRa Gateways (LoRa-to-APRS-IS Bridges)

|

Project

|

Type

|

Status

|
| --- | --- | --- |
| richonguzman/LoRa_APRS_iGate | Bidirectional LoRa-APRS-IS | Active (V3.2.3, March 2026) |
| lora-aprs/LoRa_APRS_iGate (OE5BPA) | LoRa-APRS-IS | Archived Sep 2025 |
| aprs434/lora.igate | Dual-mode (compressed + legacy) | Active |
| nakhonthai/ESP32APRS_LoRa | iGate + Digi + Tracker + WX | Active |
| sh123/esp32_loraprs | KISS TNC + iGate + Digi | Active |
| begass/LoraAPRSGW | LoRa APRS Gateway | Community |
| Mane76/LoRa_APRS_iGate_2nd_gen | iGate for LoRa32 V2.1 | Community |

How bridging works: The iGate receives LoRa APRS packets on 433.775 MHz (SF12/BW125), validates the callsign and packet
format, then forwards them as standard APRS-IS packets via TCP to an APRS-IS server (e.g., rotate.aprs2.net:14580). In
reverse, it downloads APRS-IS messages addressed to recently-heard RF stations and retransmits them over LoRa.

Global LoRa APRS standard settings: 433.775 MHz, BW 125 kHz, SF12, CR 4/5, Sync Word 0x12. This yields 293 bps data
rate. The LoRa-only APRS map shows 8,800+ active stations globally with dense coverage in Europe.

* * * * *

10\. DMR-to-APRS Bridges
------------------------

### 10.1 BrandMeister (built-in)

- URL: <https://wiki.brandmeister.network/>
- DMR radios (Anytone D878/D578, etc.) send GPS data as private call to the APRS gateway ID. BrandMeister forwards
  positions to APRS-IS. Also supports APRS text message gateway

### 10.2 FreeDMR D-APRS

- URL: <https://www.freedmr.uk/index.php/d-aprs/>
- Send APRS data as Private Call to 900999. Customizable SSID/icon/comment per DMR ID via SMS commands

### 10.3 hp3icc/D-APRS (HBLink3-based)

- URL: <https://github.com/hp3icc/D-APRS/blob/main/bridge_gps_data-SAMPLE.cfg>
- Open source iGate for DMR networks using HBLink3. Connects to APRS-IS, configurable beaconing

* * * * *

11\. Meshtastic-to-APRS Bridges
-------------------------------

### 11.1 APRStastic

- URL: <https://github.com/afourney/aprstastic> / <https://pypi.org/project/aprstastic/>
- License: Open source (MIT)
- Key features: Bidirectional Meshtastic-APRS gateway. Pre-registered device-to-callsign mapping for FCC compliance.
  Registration beaconing for gateway roaming. Runs on stock Meshtastic devices

### 11.2 meshtastic-bridge

- URL: <https://github.com/jaredquinn/meshtastic-bridge>
- License: Open source
- Key features: Plugin-based. Supports APRS-IS, Prometheus, message logging, MQTT

* * * * *

12\. Disaster Response Tracking Systems
---------------------------------------

### 12.1 ARES/RACES APRS Deployments

APRS has been deployed in numerous real disaster responses:

- Hurricane Katrina (2005): ~250 ARES members worked with relief organizations. Commercial telecommunications
  infrastructure was completely destroyed in affected areas. Amateur radio was the only communications path.
- Hurricane Maria (2017): 350 hams applied via ARRL, 50 selected, 22 deployed to Puerto Rico.
- 2017 Hurricane Season (Harvey, Irma, Maria): ARRL published comprehensive after-action review.
- New Jersey OEM: Operates "an extensive network of APRS stations to allow text messaging between all of the county
  Emergency Operating Centers in the event of the failure of conventional communications."

Key characteristics of APRS in emergencies:

|

Feature

|

Status

|
| --- | --- |
| Offline capable | Yes --- RF-only via digipeaters, no internet needed |
| Radio integration | Native --- it IS a radio system (VHF 144.390 MHz in North America) |
| Open source | Protocol is open; many open-source clients exist |
| Web-based map | Yes --- aprs.fi, APRSdirect.com (requires internet gateway) |
| Real-time vs delayed | Real-time broadcast |
| Store-and-forward | Limited --- digipeaters relay but do not queue; messages retry until timeout |

### 12.2 Winlink + APRS Integration

Winlink and APRS are meaningfully integrated for position tracking:

- Winlink Express can create GPS Position Reports that, when sent as Winlink messages, cause the sender's position to be
  plotted on all APRS system map websites including aprs.fi and APRSdirect.com
- Winlink's Hybrid Network (since 2013) offers message forwarding and delivery through a mesh-like smart network
  whenever internet connections are damaged --- true store-and-forward over HF radio
- The system supports ~12,000 radio users and handles 100,000+ monthly messages
- Position reports can be sent via HF, VHF/UHF, or any supported transport

|

Feature

|

Status

|
| --- | --- |
| Offline capable | Yes --- HF radio backbone operates without internet |
| Radio integration | Native --- HF, VHF, UHF ham frequencies |
| Open source | Client (Winlink Express) is free but not open source |
| Web-based map | Yes --- positions appear on APRS maps |
| Real-time vs delayed | Delayed (store-and-forward email model) |
| Store-and-forward | Yes --- core design principle |

### 12.3 SATERN (Salvation Army Team Emergency Radio Network)

Founded June 25, 1988. Worldwide network of volunteer amateur radio operators mobilized during disasters.

Key deployments: Hurricane Gilbert (1988), Northeast Blackout (2003), Hurricane Katrina (2005 --- handled 20,000+ health
and welfare inquiries), 2011 Joplin Tornado (hospital-to-hospital communications for ~12 hours).

Operations: VHF and HF bands (40m: 7 MHz, 20m: 14 MHz, 15m: 21 MHz). International SSB Net on 14.325 MHz. Open to all
license classes and faiths. International presence across all continents.

|

Feature

|

Status

|
| --- | --- |
| Offline capable | Yes --- pure amateur radio |
| Radio integration | Native --- HF/VHF ham radio |
| Web-based map | No --- voice nets primarily, no position tracking system |
| Store-and-forward | No --- live net operations |

### 12.4 FEMA/UN OSOCC Tracking Tools

Virtual OSOCC (UN OCHA): Real-time online coordination tool for disaster response professionals. Brings together
national authorities, UNDAC teams, and responders. Part of GDACS. Web-based, password-protected. Typically activated for
major sudden-onset disasters.

DEEP (Data Entry and Exploration Platform): Suite of tools for compiling, storing, and structuring qualitative crisis
information. NOT a position tracking system --- focuses on analytical frameworks.

|

Feature

|

Virtual OSOCC

|
| --- | --- |
| Offline capable | No --- web-based, requires internet |
| Radio integration | No |
| Open source | No --- UN-managed platform |
| Web-based map | Yes --- coordination tool |
| Store-and-forward | No |

### 12.5 Open Source Disaster Platforms

Ushahidi: Crowdsourced crisis mapping platform. Built on Laravel. Integrates with OpenStreetMap and Google Maps.
Supports SMS gateway. Deployed during Haiti earthquake, Kenya elections, Nepal earthquake. No radio/APRS integration
documented.

Sahana Eden: Free and open-source disaster management system. Deployed by governments of Sri Lanka, US, Pakistan,
Philippines, Bangladesh, India, Taiwan, China, and during the 2014 Balkans floods.

Critical finding --- APRS Integration exists: A research paper documents the *"Integration Between the APRS System and
Sahana Eden Humanitarian Management Platform for Tracking Mobile Units"*, concluding that "the integration of the APRS
into the Sahana Eden has proven to be an excellent resource for disaster mitigation as it allows for greater, more
accurate and continuous data availability." A practical guide describes running Sahana Eden on a Raspberry Pi alongside
ham radio for fully offline emergency management.

|

Platform

|

Offline

|

Radio/APRS

|

Open Source

|

Web Map

|

Real-time

|

S&F

|
| --- | --- | --- | --- | --- | --- | --- |
| Ushahidi | No | No | Yes | Yes | Crowdsourced (delayed) | No |
| Sahana Eden | Yes (local server) | Yes (documented) | Yes | Yes | Near-real-time with APRS | No |
| DEEP | No | No | Yes | No (analytical) | No | No |

### 12.6 goTenna / goTenna Pro

Founded after Hurricane Sandy destroyed 25% of East Coast cell towers. Creates mesh networking devices for off-grid text
messaging and location sharing.

goTenna Pro (for first responders): Integrates with TAK/ATAK natively. Encrypted communications. Deployment kit:
tactical briefcase with 30 devices + integrated tablet. Tested range: 23.1 miles single-hop. Proprietary --- explicitly
restricts open-source copyleft licenses.

Real deployments: Missoula County SAR (mountainous dead-zone), HARP Rescue (Hurricane Dorian recovery), Mesa County
SAR (missing hiker located using Pro X + ATAK), Texas Air National Guard TACPs.

|

Feature

|

Status

|
| --- | --- |
| Offline capable | Yes --- core design (mesh, no infrastructure) |
| Radio integration | Is itself a radio mesh; integrates with ATAK |
| Open source | No --- proprietary (Meshtastic is the open-source alternative) |
| Web-based map | No --- app-based only |
| Store-and-forward | Yes (mesh relay) |

### 12.7 Garmin inReach / SPOT

Satellite-based position tracking for disaster response. Garmin inReach Mini 2 uses Iridium network (global coverage, no
cell dependency). Tracking intervals: 2.5--60 minutes. Battery: up to 14 days. Interactive SOS to Garmin Response (24/7
staffed). SPOT X provides similar capability with its own US mobile number.

Both are fully offline (satellite), but neither has radio integration or is open source. No store-and-forward (direct
satellite uplink).

### 12.8 TAK (Team Awareness Kit) / ATAK

TAK is a US government-developed Common Operating Picture (COP) system used by military and increasingly by first
responders. CivTAK is the civilian version.

Core capabilities: Real-time position tracking of all team members on shared map, chat, file sharing, streaming. Works
on Android (ATAK), iOS (iTAK), Windows (WinTAK). Offline maps supported. TAK Server brokers and stores data; supports
federation between servers.

Open source options: FreeTAKServer (Python3, Eclipse Public License) --- runs from AWS down to Android devices, REST
API, federation, KML, SSL, data packages.

APRS Integration --- CRITICAL:

1. APRSTAK (github.com/pinztrek/aprstak): Python tool that reads APRS-IS position reports and injects them into TAK
   servers as CoT (Cursor on Target) XML messages. GPL-3.0.
2. APRSCOT (aprscot.readthedocs.io): Converts APRS station data to CoT messages for display on TAK mapping platforms.

Meshtastic + TAK Integration: FreeTAKHub Meshtastic (github.com/FreeTAKTeam/FreeTAKHub_meshtastic) uses MQTT + Node-RED
to bridge Meshtastic mesh networks with FreeTAKServer. Native Meshtastic iOS TAK integration exists. OpenTAKServer has
Meshtastic support. Mesh2TAK node provides bidirectional bridge.

|

Feature

|

TAK/ATAK

|

E7APRS Tracker

|
| --- | --- | --- |
| Platform | Native Android/iOS/Windows apps | Web browser (any device) |
| Setup complexity | High (server + clients + certificates) | Low (URL in browser) |
| Offline maps | Yes (downloaded tiles) | Possible (service workers) |
| Position sources | GPS + CoT from any connected system | APRS-IS, APRS.fi, DMR, LoRa relay, simulator |
| Mesh/radio integration | Meshtastic, goTenna, military radios | LoRa relay, APRS-IS TCP, DMR parser |
| Store-and-forward | Via Meshtastic mesh | Yes (LoRa relay NDJSON queue) |
| Open source | FreeTAKServer is; official TAK is not fully open | Yes (fully open) |
| Target audience | Military, first responders, SAR | Ham radio operators, civil protection |
| Federation | Yes (multi-server) | No (single backend) |
| Two-way messaging | Yes (GeoChat) | No (view-only map) |
| Learning curve | Steep | Minimal |

### 12.9 Real Deployments in the Balkans

Bosnia and Herzegovina --- RMZO E7 LoRa APRS Network

ARAuBiH (Association of Radio Amateurs in BiH) has deployed and is expanding a LoRa APRS iGate network across Bosnia and
Herzegovina, specifically for emergency communications (RMZO = Radio Emergency Communications).

- E70ARA-2: LoRa APRS 433 MHz iGate in Sarajevo (Ilidza), operating as both EMCOMM DIGIPEATER and iGate
- E7HQ-1: LoRa APRS 433 MHz Digi-iGate-WX on Mountain Smetovi (1001m ASL)
- E75EMA-10: New APRS iGate in Borik, Maglaj
- Coverage: Ilidza, Dobrinje, Novi Grad Sarajevo, Istočno Sarajevo, Lukavica, Stup, highway A1, Lašvan basin, Tarčin
  A1-M17 to Koševo
- Hardware: Heltec Wireless Tracker devices
- RPT Manager: Alen Ruvic (E71AR) --- coordinator of the 2024 flood response
- Completed: Six-month testing phase before operational deployment

2024 Bosnia Flooding (October 4, 2024): Over 20 deaths, 40+ missing. E71AR (Alen Ruvic) coordinated amateur radio
emergency response. Frequencies: 3.612 MHz, 7.150 MHz (HF), 145.675 MHz, 145.500 MHz (VHF).

2014 Balkans Floods: Worst flooding in Balkan in over 100 years, affecting Serbia, Croatia, and BiH. ~24,000 evacuated
in Serbia, 30,000 displaced in BiH. Alliance of Serbian Radio Amateurs (SRS) coordinated response. ~25,000 traffic
pieces handled. No APRS involvement documented.

### 12.10 Academic Research on Offline Position Tracking

1. "Design of Emergency Position Reporting System for Disasters Using Amateur Radio and APRS" (2024) --- Proposes
   APRS-based system for use "after the Internet and mobile communication system infrastructure are
   destroyed." [ResearchGate](https://www.researchgate.net/publication/377703201)
2. "Emergency Position Reporting System for Border and Inland Areas with APRS" (IEEE, 2024) --- Addresses communication
   challenges in remote regions using APRS Tracker, Digipeater, and Internet
   Gateway. [IEEE](https://ieeexplore.ieee.org/document/10768034)
3. "BPoL: A Disruption-Tolerant LoRa Network for Disaster" (PEASEC, 2023) --- Implements store-and-forward messaging
   over LoRa. Key finding: store-and-forward improves reliability over pure mesh in degraded
   conditions. [PDF](https://peasec.de/paper/2023/2023_SchmidtKuntkeBauerBaumgaertner_BPOL_GHTC.pdf)
4. "Enhancing Situational Awareness with LoRa Mesh Networks" (2024) --- Examines Meshtastic firmware for decentralized
   mesh networking and location tracking. [ResearchGate](https://www.researchgate.net/publication/385341128)
5. "Solar-Powered LoRa Mesh Network for Emergency Communication" (IJARCCE, 2025) --- Solar-powered, decentralized mesh
   using LoRa for infrastructure-independent
   operation. [PDF](https://ijarcce.com/wp-content/uploads/2025/12/IJARCCE.2025.141266-Solar.pdf)
6. LOCATE (University of Bologna): LoRa-based wearable IoT system with BLE Android app, multi-hop dissemination
   algorithm, GPS-free trilateration via LoRa
   RSSI. [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1570870518309004)
7. "LoRa for Mountain SAR" (IEEE, 2020) --- Body-worn LoRa devices in South Tyrol mountain environments, path-loss
   models for person localization. [IEEE](https://ieeexplore.ieee.org/document/9169667/)
8. "Multi-Hop LoRa for Disaster Management" (IEEE, 2023) --- Field test of D2D LoRa network interfacing with Civil
   Protection WebGIS (ARCHIMEDE) in Calabria, Italy. [IEEE](https://ieeexplore.ieee.org/document/10286960/)
9. "Titan DMS" (IEEE, 2024) --- SAR robot communication via LoRa in damaged infrastructure
   scenarios. [IEEE](https://ieeexplore.ieee.org/document/10614393/)
10. "LoRa-Based Smartphone Communication for Crisis" (ISCRAM, 2020) --- GPS position via BLE to LoRa companion
    device. [PDF](https://idl.iscram.org/files/jonashochst/2020/2291_JonasHochst_etal2020.pdf)
11. "UAV-assisted LoRa SAR" (IEEE TMC, 2024) --- Flying LoRa gateways overcoming terrestrial blockages in remote
    areas. [ACM](https://dl.acm.org/doi/abs/10.1109/TMC.2024.3468382)

### 12.11 OASIS CAP (Common Alerting Protocol)

CAP v1.2 is an XML-based data format for exchanging emergency alerts. Not a position tracking system, but encodes
geographic data (polygon, circle, geocode, altitude/ceiling) in WGS 84 (EPSG:4326). Adopted by FEMA IPAWS (since 2010),
Canada Alert Ready (since 2015), Italy Fire Corps (~25,000 CAP messages daily), and WMO for international weather
alerts. Relevant if E7APRS ever needs to receive/display emergency alerts on the map.

* * * * *

13\. Multi-Source Aggregation and Real-Time Architectures
---------------------------------------------------------

### 13.1 Multi-Protocol Tracking Aggregators

Plane/Sailing by Ian Renton: The best open-source example of a true multi-protocol tracker combining ADS-B + AIS +
APRS + Radiosonde + Meshtastic LoRa.

- Server: Java-based, ingests from Dump1090 (ADS-B), AIS Catcher (UDP NMEA-0183), Dire Wolf (KISS TCP for APRS),
  radiosonde_auto_rx (HORUS JSON UDP), Meshtastic (Python API)
- Aggregation: Normalizes all heterogeneous data into a common track format, handles persistence times, exposes unified
  HTTP JSON API
- Frontend: Client JavaScript renders tracks on a map with MIL-STD2525C symbols
- Hardware: Raspberry Pis with RTL-SDR dongles
- <https://ianrenton.com/projects/planesailing/>

FlightAirMap: Open-source (AGPL v3) PHP application combining ADS-B, AIS, APRS, ACARS, and virtual airlines (VATSIM,
IVAO). Backend: PHP with MySQL/MariaDB/PostgreSQL+PostGIS. Outputs GeoJSON, CZML, KML, GPX, CSV, RSS.

- <https://github.com/Ysurac/FlightAirMap> --- 584 stars

MarineTraffic (now Kpler): Commercial. 13,000+ AIS receivers globally. Architecture uses SOA with socket-based
connections and IEC AIS standardized protocol. Database supports both PostgreSQL (concurrent) and SQLite (portable) via
their AISdb library.

- <https://arxiv.org/html/2407.08082v1>

| System        | Sources                        | Real-time method               | Storage                  | Offline? | Open source? |
|---------------|--------------------------------|--------------------------------|--------------------------|----------|--------------|
| aprs.fi       | APRS-IS + AIS                  | Web map refresh                | Server-side DB           | No       | No           |
| Plane/Sailing | ADS-B+AIS+APRS+Radiosonde+LoRa | HTTP JSON API polling          | Disk persistence         | Yes      | Yes (GPL)    |
| FlightAirMap  | ADS-B+AIS+APRS+ACARS+VA        | PHP daemons + periodic refresh | PostgreSQL+PostGIS       | No       | AGPL v3      |
| MarineTraffic | AIS (terrestrial+satellite)    | Streaming + API                | PostgreSQL / proprietary | No       | No           |

### 13.2 Real-Time Streaming Architecture Patterns

Uber's Architecture (gold standard):

- Data flow: Driver GPS → mobile app (protobuf over TLS/gRPC) → Gateway → Kafka (partitioned by geography)
- Hot path (real-time): Kafka → Redis cluster (DriverState, H3 cell membership with TTL, ephemeral locks) → Matching via
  H3 spatial index → RAMEN (persistent WebSocket with monotonic sequence numbers)
- Cold path (analytics): Kafka → Flink streaming → supply/demand aggregates per H3 cell → ETA models
- Key numbers: Millions of location events/minute; sub-second Redis reads; H3 reduced matching from 10--15s to under 3s
  at 1M+ req/s

Protocol Comparison for Tracking:

| Factor | WebSocket/Socket.IO | MQTT | SSE |
|---|---|---|---|
| Direction | Bidirectional | Bidirectional (pub/sub) | Server-to-client only |
| Best for | Browser/mobile apps | IoT devices, vehicles | Dashboards, feeds |
| Overhead per message | ~2 bytes/frame | Binary, minimal | ~5 bytes |
| Auto-reconnect | Manual (Socket.IO adds it) | Built-in | Built-in |
| Browser support | Native | No (needs WS bridge) | Native |
| QoS levels | No | Yes (0, 1, 2) | No |

Dominant production pattern (hybrid):

| Layer | Protocol | Purpose |
|---|---|---|
| Vehicle/device → Cloud | MQTT | Lightweight telemetry over cellular |
| Backend processing | Kafka + Flink | Stream processing, geospatial indexing |
| Cloud → Browser | WebSocket (Socket.IO) | Real-time map updates |
| Cloud → Dashboard | SSE | One-way monitoring |


### 13.3 Three-Tier Storage Patterns

The E7APRS architecture (in-memory Map → SQLite → Supabase) maps to a recognized tiered storage taxonomy:

| Tier | Label | Technology | Latency | Durability |
|---|---|---|---|---|
| 0 | Hot | In-memory (Redis, JavaScript Map) | Sub-ms | Volatile |
| 1 | Warm | Local persistent DB (SQLite, PostgreSQL) | Ms | Durable |
| 2 | Cold | Cloud backup (Supabase, S3) | Network-bound | Disaster recovery |

Industry precedents:

- SingleStore: Memory tier (Rowstore) → Persistent Cache (Columnstore) → Cloud Storage (durable object storage)
- Uber: Redis (hot, ephemeral with TTL) → Kafka (durable event log) → Cassandra (cold storage for analytics)

### 13.4 SQLite + Cloud Backup Patterns

The "SQLite Renaissance" includes several tools relevant to E7APRS's dual-database architecture:

| Tool | Pattern | Replication method | Multi-node? | Open source? |
|---|---|---|---|---|
| Litestream | SQLite → S3 backup | WAL streaming | No (single primary) | Yes (Apache 2.0) |
| LiteFS | SQLite cluster | FUSE + transaction shipping | Yes (primary/follower) | Yes (Apache 2.0) |
| Turso/libSQL | SQLite + cloud sync | WAL frame sync | Yes (primary/follower) | Yes (MIT) |
| Cloudflare D1 | Managed SQLite at edge | Distributed replication | Yes (distributed) | No |
| **E7APRS** | SQLite + Supabase dual-write | Application-level | No (single primary) | Yes |

E7APRS's approach is a simpler, more explicit version of what Litestream automates at the WAL level. The advantage is
full control over what gets mirrored and when; the disadvantage is maintaining dual-write logic.

### 13.5 Open Source Fleet Tracking Platforms

Traccar: The dominant open-source GPS tracking system. Java backend, 200+ GPS protocols, 2000+ device models. REST API +
WebSocket stream. React + Material UI + MapLibre frontend. PostgreSQL/MySQL storage. Apache 2.0 license. 5,400+ stars.

- <https://github.com/traccar/traccar>

OpenGTS: First open-source GPS fleet tracking system. MySQL (MyISAM). Feature-rich but aging.

OpenRemote: 100% open-source IoT platform (AGPL v3). Fleet management on broader IoT. Java/Groovy/TypeScript.
PostgreSQL. MQTT + HTTP + WebSocket APIs. Multi-tenancy. Rules engine. Docker deployment.

| Platform | Protocols | Storage | Real-time | Multi-source? | License |
|---|---|---|---|---|---|
| Traccar | 200+ GPS protocols | PostgreSQL/MySQL | REST + WebSocket | Yes (any supported device) | Apache 2.0 |
| OpenGTS | Limited set | MySQL (MyISAM) | Web polling | Yes | Open source |
| OpenRemote | MQTT + REST + Modbus + BACnet | PostgreSQL | MQTT + WebSocket + HTTP | Yes (protocol agents) | AGPL v3 |
| **E7APRS** | APRS-IS + APRS.fi + DMR + LoRa + Simulator | SQLite + Supabase | Socket.IO | Yes (source-tagged) | — |

### 13.6 MQTT-Based IoT Tracking (LoRa Platforms)

ChirpStack: Open-source LoRaWAN network server. Network server + application server + device management. All application
data published to MQTT by default. Web interface for gateway/device/tenant management.

- <https://www.chirpstack.io/>

The Things Network (TTN): Global community-driven free LoRaWAN network. Cloud-hosted. MQTT integration for downstream
applications.

Helium Network: Decentralized LoRaWAN network, largest in the world (hundreds of thousands of hotspots).
Blockchain-incentivized. Data Credits at $0.00001/DC.

Typical data flow: GPS tracker → LoRa transceiver → LoRaWAN Gateway → Packet Forwarder → Gateway Bridge (MQTT/UDP) →
ChirpStack/TTN → MQTT Broker → Application backend → Dashboard

E7APRS's LoRa relay module implements a similar pattern to Meshtastic's MQTT bridge but with a custom compact binary
codec (~35 bytes/position) optimized for LoRa packet size constraints. The store-and-forward NDJSON queue is
architecturally similar to MQTT's retained messages + offline session queuing but implemented at the application layer
for greater control.

### 13.7 Academic Papers on Real-Time Position Aggregation

1. "An Overview of Data Aggregation Architecture for Real-Time Tracking with Sensor Networks" (He et al.) --- Describes
   aggressive data aggregation for physical tracking systems where communication efficiency is
   paramount. [IEEE](https://ieeexplore.ieee.org/document/1639413/)
2. "Multi-sensor Integrated Navigation/Positioning Systems Using Data Fusion" (Information Fusion, 2023) ---
   Comprehensive survey of multi-sensor data fusion for integrated
   positioning. [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1566253523000398)
3. "Data Integration and Storage Strategies in Heterogeneous Analytical Systems" (MDPI Information, 2025) ---
   Cross-layer taxonomy linking integration mechanisms to storage/query
   substrates. [MDPI](https://www.mdpi.com/2078-2489/16/11/932)
4. "Maritime Tracking Data Analysis and Integration with AISdb" (arXiv, 2024) --- Describes dual-database architecture
   for AIS data: PostgreSQL (concurrent) and SQLite (portable). [arXiv](https://arxiv.org/html/2407.08082v1)
5. "LoRa Mesh-Based IoT GPS Tracking System for Mountain Climbers" (IIETA, 2024) --- Uses Meshtastic LoRa devices for
   GPS tracking where cellular is unavailable. [IIETA](https://iieta.org/journals/ijsse/paper/10.18280/ijsse.140610)

* * * * *

14\. Comparison Matrix
----------------------

| Feature                    | aprs.fi                    | Track Direct               | APRStac           | YAAC         | Xastir       | SARTrack                             | **E7APRS**                                                     |
|----------------------------|----------------------------|----------------------------|-------------------|--------------|--------------|--------------------------------------|----------------------------------------------------------------|
| Multi-source aggregation   | Partial (APRS-IS/CWOP/AIS) | Partial (APRS-IS/CWOP/OGN) | RF + Meshtastic   | RF + APRS-IS | RF + APRS-IS | APRS + commercial radios + satellite | **Yes (APRS-IS + APRS.fi API + DMR + LoRa relay + simulator)** |
| WebSocket real-time        | Yes (proprietary)          | Yes                        | Yes (LAN)         | No (desktop) | No (desktop) | No (desktop)                         | **Yes (Socket.io)**                                            |
| Works offline              | iOS app via BLE TNC        | No                         | Partial           | Yes          | Yes          | Yes                                  | **Yes (LoRa relay module)**                                    |
| Own backend + database     | Yes (closed)               | Yes (PostgreSQL)           | Local             | Local        | Local        | Local + servers                      | **Yes (SQLite + Supabase)**                                    |
| Open source                | No                         | Yes                        | Yes               | Yes          | Yes          | No                                   | **Yes**                                                        |
| DMR integration            | Via BrandMeister only      | No                         | No                | No           | No           | No                                   | **Yes (native DMR parser)**                                    |
| LoRa relay / store-forward | No                         | No                         | Meshtastic bridge | No           | No           | No                                   | **Yes (binary codec, NDJSON queue, transport abstraction)**    |
| Web-based map              | Yes                        | Yes                        | Yes               | No           | No           | No (desktop maps)                    | **Yes (Next.js + Leaflet)**                                    |
| Self-hosted                | No                         | Yes                        | Yes               | Yes          | Yes          | Partial                              | **Yes**                                                        |
| Auth / user profiles       | No                         | No                         | No                | No           | No           | No                                   | **Yes (Supabase Auth, profiles)**                              |

* * * * *

15\. Key Differentiators of E7APRS
----------------------------------

### 15.1 No Other System Combines These Features

E7APRS occupies a unique niche that no single existing system covers:

1. Native DMR audio parsing (DSD+ stdout) as a first-class data source --- no existing tracker does this
2. LoRa store-and-forward relay with binary codec, persistent NDJSON queue, and transport abstraction (
   mock/TCP/serial) --- designed for Civil Protection scenarios where internet is unavailable. No other open-source LoRa
   APRS project has persistent store-and-forward
3. Multi-source aggregation (APRS-IS, APRS.fi API, DMR, LoRa relay, simulator) with source-specific marker colors in a
   single web dashboard
4. Modern web stack (Next.js 14 + Socket.io + Leaflet) with real-time WebSocket push and authentication --- most
   self-hosted alternatives use older Python/PHP/Angular stacks
5. Dual database (local SQLite primary + Supabase backup) with warm cache on startup --- three-tier storage pattern (
   hot/warm/cold)

### 15.2 LoRa Relay: Architectural Uniqueness

| Capability | Meshtastic S&F | Semtech CircularFS | MeshCore Room Server | **E7APRS Relay** |
|---|---|---|---|---|
| Persistent queue | No (PSRAM, volatile) | Yes (NOR Flash FIFO) | Yes (message history) | **Yes (NDJSON file-backed FIFO)** |
| Crash-safe | No | Yes | Partial | **Yes (append-only)** |
| Position data | No (text only) | N/A | N/A | **Yes (primary use case)** |
| Transport abstraction | No | No | No | **Yes (ITransport: mock/TCP/serial)** |
| Binary batch encoding | No | No | No | **Yes (0xAE70 framed, ~3 pos/packet)** |
| Link simulation | No | No | No | **Yes (packet loss, latency, throttle, disconnect)** |
| Webhook integration | No | No | No | **Yes** |

### 15.3 Architecture Pattern Mapping

| Pattern | Industry Precedent | E7APRS Implementation |
|---|---|---|
| Multi-source aggregation with source tagging | FlightAware HyperFeed, Plane/Sailing, FlightAirMap | APRS-IS + APRS.fi + DMR + LoRa + Simulator, each tagged |
| WebSocket real-time push with full-state snapshot on connect | Uber RAMEN, Socket.IO standard | Socket.IO with immediate state push |
| Three-tier storage (hot/warm/cold) | Uber (Redis/Kafka/Cassandra), SingleStore | In-memory Map / SQLite / Supabase |
| SQLite primary + cloud mirror | Turso Embedded Replicas, Litestream | Application-level dual-write |
| Store-and-forward offline relay | MQTT retained messages, AWS Greengrass, Meshtastic MQTT bridge | NDJSON file-backed queue + binary codec over LoRa |
| Transport abstraction | Traccar protocol decoders, ChirpStack Gateway Bridge | ITransport interface (mock/TCP/serial) |

The closest open-source analog to the full E7APRS system is Plane/Sailing (multi-protocol aggregation into unified web
view) combined with Traccar (device protocol abstraction + web tracking), but neither has E7APRS's LoRa
store-and-forward relay capability or three-tier storage pattern.

### 15.4 Natural Integration Partners

The RMZO E7 LoRa APRS network in BiH (E70ARA, E7HQ, E75EMA stations) managed by Alen Ruvic (E71AR) is the most directly
relevant real-world deployment and a natural potential integration partner. This network already uses Heltec Wireless
Tracker devices on 433.775 MHz and operates specifically for civil protection emergency communications.

* * * * *

16\. Sources
------------

### APRS Web Trackers

- <https://aprs.fi/>
- <https://github.com/qvarforth/trackdirect>
- <https://www.aprsdirect.com/>
- <https://aprs-map.info/>
- <https://aprs.to/>
- <https://www.findu.com/>
- <https://aprs.no/>
- <https://aprsdirect.de/>
- <https://lora-aprs.live/>
- <https://www.gliderradar.com/>

### Desktop Clients

- <https://www.ka2ddo.org/ka2ddo/YAAC.html>
- <https://github.com/Xastir/Xastir>
- <http://aprsisce.wikidot.com/>
- <https://www.pinpointaprs.com/>
- <https://www.ui-view.net/>
- <https://www.sartrack.co.nz/>

### Mobile Apps

- <https://aprsdroid.org/>
- <https://github.com/ge0rg/aprsdroid>
- <https://apps.apple.com/us/app/aprs-fi/id922155038>
- <https://apps.apple.com/us/app/aprs-pro-deluxe/id1163741288>
- <https://apps.apple.com/us/app/aprskit-live-aprs-tracker/id6744252867>
- <https://www.pulsemodem.com/>

### Self-Hosted Infrastructure

- <https://github.com/hessu/aprsc>
- <http://www.aprs-is.net/javaprssrvr/>
- <https://github.com/PhirePhly/aprx>

### Open-Source Web Dashboards

- <https://github.com/qvarforth/trackdirect>
- <https://github.com/ccmolik/aprsweb>
- <https://github.com/trasukg/local-aprs>
- <https://github.com/PC7MM/Direwolf-APRS-Web-Dashboard>
- <https://github.com/japalie/aprs-frontent>
- <https://www.aprstac.com/>
- <https://github.com/ha5dzs/nyuad-aprs-server>

### Software Modems

- <https://github.com/wb2osz/direwolf>
- <https://github.com/jketterl/openwebrx>

### LoRa APRS Projects

- <https://github.com/lora-aprs/LoRa_APRS_Tracker>
- <https://github.com/lora-aprs/LoRa_APRS_iGate>
- <https://github.com/richonguzman/LoRa_APRS_Tracker>
- <https://github.com/richonguzman/LoRa_APRS_iGate>
- <https://github.com/SQ9MDD/TTGO-T-Beam-LoRa-APRS>
- <https://github.com/sh123/esp32_loraprs>
- <https://github.com/nakhonthai/ESP32APRS_LoRa>
- <https://github.com/lightaprs>
- <https://github.com/aprs434/lora.tracker>
- <https://github.com/aprs434/lora.igate>
- <https://aprs438.readthedocs.io/>

### Meshtastic and Mesh

- <https://meshtastic.org/>
- <https://github.com/meshtastic>
- <https://github.com/meshcore-dev/MeshCore>
- <https://meshcore.co.uk/>

### DMR/Meshtastic Bridges

- <https://wiki.brandmeister.network/>
- <https://www.freedmr.uk/index.php/d-aprs/>
- <https://github.com/hp3icc/D-APRS>
- <https://github.com/afourney/aprstastic>
- <https://github.com/jaredquinn/meshtastic-bridge>

### Disaster Response

- <https://www.salvationarmyusa.org/satern-program/>
- <https://vosocc.unocha.org/>
- [www.ushahidi.com](http://www.ushahidi.com/)
- <https://github.com/sahana/eden>
- <https://www.researchgate.net/publication/330263460>
- <https://gotennapro.com/>
- <https://www.garmin.com/en-US/p/765374>
- <https://www.findmespot.com/>
- <https://github.com/FreeTAKTeam/FreeTakServer>
- <https://github.com/pinztrek/aprstak>
- <https://arabih.ba/lora-aprs-igate-433-sarajevo/>
- <https://www.iaru-r1.org/2024/amateur-radio-responds-to-flooding-in-bosnia/>

### Academic Papers

- <https://www.researchgate.net/publication/377703201>
- <https://ieeexplore.ieee.org/document/10768034>
- <https://peasec.de/paper/2023/2023_SchmidtKuntkeBauerBaumgaertner_BPOL_GHTC.pdf>
- <https://www.researchgate.net/publication/385341128>
- <https://ijarcce.com/wp-content/uploads/2025/12/IJARCCE.2025.141266-Solar.pdf>
- <https://www.sciencedirect.com/science/article/abs/pii/S1570870518309004>
- <https://ieeexplore.ieee.org/document/9169667/>
- <https://ieeexplore.ieee.org/document/10286960/>
- <https://ieeexplore.ieee.org/document/10614393/>
- <https://dl.acm.org/doi/abs/10.1109/TMC.2024.3468382>
- <https://ieeexplore.ieee.org/document/1639413/>
- <https://www.sciencedirect.com/science/article/pii/S1566253523000398>
- <https://www.mdpi.com/2078-2489/16/11/932>
- <https://arxiv.org/html/2407.08082v1>

### Multi-Source Architectures

- <https://ianrenton.com/projects/planesailing/>
- <https://github.com/Ysurac/FlightAirMap>
- <https://github.com/traccar/traccar>
- <https://github.com/openremote/openremote>
- <https://www.chirpstack.io/>
- <https://litestream.io/>
- <https://github.com/superfly/litefs>
- <https://turso.tech/>
- <https://github.com/uber/h3>
- docs.oasis-open.org/emergency/cap/v1.2/CAP-v1.2-os.html

* * * * *

*Document compiled from research across 193 web sources, academic databases, and GitHub repositories.*