# EmbryoGuard Pro 🥚📡

**IoT-Based Smart Embryo Incubator System** — a dual-node ESP32 embedded system that automates temperature, humidity, air quality, and egg-turning for poultry incubation, with a real-time Flask + WebSocket web dashboard.

Built as an Embedded Systems course project (Instructor: Dr. Safa Elaskary).

---

## ✨ Features

- **Dual-node architecture** — two ESP32 cores each running an independent FreeRTOS task:
  - **Node 1 (Climate Control):** DHT22 sensor, relay-controlled fan + heat lamp, servo-driven egg turner
  - **Node 2 (Environmental Monitor):** DHT22, MQ-135 air-quality sensor, LDR light sensor
- **Multi-species support** — dynamic threshold profiles for Chicken, Duck, Quail, and Goose eggs
- **Real-time web dashboard** — Flask + Socket.IO pushes live sensor data to the browser with no page refresh
- **AUTO / MANUAL control modes** with safety-override alerts (danger/warning thresholds for temperature, humidity, ammonia, and light)
- **Persistent data logging** — every reading is stored in a SQLite database for historical analysis
- **Mobile-friendly access** — QR code endpoint (`/api/server-info`) for quick phone connection to the dashboard

## 🏗️ System Architecture

```
ESP32 Node 1 ──┐                     ┌── Browser (real-time charts,
 (Climate)     │   HTTP POST         │    alerts, manual controls)
               ├──► /api/data ──► Flask Server ──► WebSocket ──┤
ESP32 Node 2   │                (SocketIO + SQLite)             │
 (Monitor)   ──┘                     └── Browser polls /api/state on load
```

- Node 1 & 2 POST sensor readings to `/api/data` every 3 seconds
- Flask broadcasts updates to all connected browsers via WebSocket
- Browser control actions are queued as commands and polled by the ESP32 via `/api/commands/<node_id>`

## 🔌 Hardware & Pinout

| Node | Component | GPIO |
|---|---|---|
| Node 1 | DHT22 (temp/humidity) | 21 |
| Node 1 | Relay — Fan (active-low) | 27 |
| Node 1 | Relay — Lamp (active-low) | 26 |
| Node 1 | Servo (egg turner) | 13 |
| Node 2 | DHT22 (temp/humidity) | 22 |
| Node 2 | MQ-135 (analog out) | 32 |
| Node 2 | MQ-135 (digital out) | 17 |
| Node 2 | LDR (light sensor) | 33 |

**Board:** LILYGO TTGO T-Display ESP32 (or standard ESP32 DevKit)

## 🛠️ Tech Stack

- **Firmware:** C++ (Arduino framework), FreeRTOS, ESP32Servo, ArduinoJson, DHT sensor library
- **Backend:** Python, Flask, Flask-SocketIO, SQLite
- **Frontend:** HTML, CSS, JavaScript, Socket.IO client, real-time charting

## 📸 Screenshots

See the [`images/`](images) folder for dashboard screenshots and hardware build photos.

## 🚀 Getting Started

### 1. Firmware (ESP32)

1. Open `firmware/incubator_merged.ino` in the Arduino IDE
2. Install the required libraries: `DHT sensor library`, `ESP32Servo`, `ArduinoJson`
3. **Before uploading**, edit these lines with your own network details:
   ```cpp
   const char* WIFI_SSID     = "YOUR_WIFI_NAME";
   const char* WIFI_PASSWORD = "YOUR_WIFI_PASSWORD";
   const char* DASHBOARD_URL = "http://YOUR_COMPUTER_IP:8080";
   ```
4. Flash to your ESP32 board(s)

### 2. Dashboard (Server)

```bash
# Clone the repo
git clone https://github.com/mirnawalid/EmbryoGuard-Pro.git
cd EmbryoGuard-Pro/dashboard

# Install dependencies
pip install -r requirements.txt

# Run the server
python app.py
```

The dashboard will be available at `http://<your-computer-ip>:8080`. The ESP32 nodes must be on the same WiFi network and configured with this IP in `DASHBOARD_URL`.

## 📁 Project Structure

```
EmbryoGuard-Pro/
├── README.md
├── .gitignore
├── requirements.txt
├── firmware/
│   └── incubator_merged.ino      # Dual-core ESP32 firmware
├── dashboard/
│   ├── app.py                    # Flask + SocketIO server
│   ├── requirements.txt
│   ├── templates/
│   │   └── index.html
│   └── static/
│       ├── css/style.css
│       ├── js/dashboard.js
│       └── images/chicken_pattern.png
├── docs/
│   └── EmbryoGuard_Pro_Team_Report.pdf
└── images/
    ├── wiring/                   # Circuit/wiring photos
    ├── dashboard/                # Dashboard UI screenshots
    └── build/                    # Finished hardware photos
```

## 👥 Team

Report divided among 5 team members covering: system architecture, firmware/FreeRTOS design, dashboard development, communication protocol, and database/logging. Full breakdown in [`docs/EmbryoGuard_Pro_Team_Report.pdf`](docs/EmbryoGuard_Pro_Team_Report.pdf).

## 📬 Contact

**Mirna Walid**
📧 mws103561@gu.edu.eg
📞 +20 109031364
🔗 [LinkedIn](https://linkedin.com/in/mirna-walid-145a163b5)
💻 [GitHub](https://github.com/mirnawalid)
