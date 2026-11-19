# Raspberry Pi Sense HAT Weather Station

An Internet of Things (IoT) environmental monitoring system built with a **Raspberry Pi**, **Sense HAT**, and **Node-RED** for the **MakerLab 202** module at ATU Sligo (submitted April 15, 2026). The system collects real-time temperature, humidity, and atmospheric pressure readings and visualises them on a local Node-RED web dashboard (`:1880/ui`).

## Architecture & Data Flow

```text
[ Raspberry Pi + Sense HAT ]
          │  (Environment events: temperature, humidity, pressure)
          ▼
[ Node-RED Runtime (:1880) ]
          ├──► Function Nodes (extract msg.payload.temperature / .humidity / .pressure)
          │         ├──► Bar Chart Results  (Temperature, Pressure, Humidity)
          │         ├──► Line Chart Results (Temperature, Pressure, Humidity)
          │         └──► Gauge Results      (Temperature, Pressure, Humidity)
          ▼
[ Local Web Dashboard (:1880/ui) ]  ──► Dark theme ("Adam's Weather Station")
```

## Hardware & Software Stack

- **Hardware:** Raspberry Pi, Sense HAT module (temperature, humidity, pressure sensors, LED matrix)
- **OS & Remote Access:** Raspberry Pi OS (Debian-based Linux, `arm64`), WayVNC / TigerVNC, `ifconfig` / local IP networking
- **Applications & Nodes:**
  - **Node-RED** (v4.1.5 on Node.js v20.20.0, running on port `1880`)
  - `node-red-dashboard` (UI gauges, line charts, bar charts, layout groups, and dark theme)
  - `node-red-node-pi-sense-hat` (v0.2.0 – `rpi-sensehat in` node configured for environment events)

## Setup & Configuration Summary

1. **System Preparation:** Connected the Raspberry Pi to Wi-Fi and updated packages via `sudo apt-get update`, `sudo apt-get -y upgrade`, `sudo apt dist-upgrade`, and `sudo reboot`.
2. **Node-RED Installation & Launch:** Installed Node-RED using the official Raspberry Pi installer script, launched it from **Programming → Node-RED**, and checked the Pi's IP address (`ifconfig`) to access the editor at `http://<pi-ip>:1880`.
3. **Palette & Library Setup:** Installed `node-red-dashboard` and `node-red-node-pi-sense-hat` via **Menu → Manage palette → Install**, restarted the Node-RED service (`node-red-stop` / `node-red-start`), and checked the `sensehat.js` configuration (`Python 2.7` → `Python 3` compatibility where needed).
4. **Flow & Payload Extraction:** Added `Sense HAT` input nodes with **Environment events** enabled (unchecking motion and joystick events), paired each metric with a `function` node extracting the payload field:
   ```javascript
   var o = msg.payload;
   msg.payload = o.temperature; // or o.humidity / o.pressure
   return msg;
   ```
5. **Dashboard Layout & Styling:** Organised widgets under **Adam's Weather Station** into **Bar Chart Results**, **Line Chart Results**, and **Gauge Results** groups, configured units (`°C`, `hPa`, humidity), switched to the **Dark** theme, set the site title to **Adam's Weather Station**, deployed the flow, and viewed live output at `localhost:1880/ui` (or `<pi-ip>:1880/ui`).

## Challenges & Troubleshooting

- **Python / Sense HAT Integration:** Ensured the Sense HAT node and environment worked cleanly with Python 3 and restarted the Node-RED service after palette changes and `sensehat.js` edits.
- **Data Formatting & Update Lag:** Resolved occasional misrouted or delayed readings by verifying function node property extraction (`o.temperature`, `o.humidity`, `o.pressure`), dashboard group/type assignments, and periodic refresh/lag behaviour through iterative testing.

## Repository Contents

- `README.md` — Project overview, architecture, setup steps, and troubleshooting notes
- `weather_station_flow.json` — Exportable Node-RED flow structure and function-node payload mapping for the weather dashboard
- `docs/` — Optional location for your original MakerLab 202 report (`Individual Project Part C - ADAM.pdf`) and dashboard screenshots



