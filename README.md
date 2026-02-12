# SHS01 – XIAO ESP32-C6 + LD2410 (Zigbee mmWave Presence Sensor)

> **Board-specific fork** of the SHS01 DIY Zigbee mmWave presence sensor firmware, adapted for **Seeed Studio XIAO ESP32-C6** + **Hi-Link LD2410 / LD2410C**.  
> Works with **Zigbee2MQTT** (Z2M). **Occupancy reporting is reliable**; moving/static targets are exposed as custom attributes (requires correct converter mapping).

---

## ✅ Highlights

- **Zigbee Router** device (strengthens your mesh network)
- **mmWave presence** using LD2410 UART output
- Exposes entities:
  - **Occupancy**
  - **Moving target**
  - **Static target**
- Z2M-friendly: pairs, exposes controls, and reports state changes
- Easy build with **ESP-IDF**

---

## 📌 Table of Contents

- [Hardware](#-hardware)
- [Wiring](#-wiring)
- [Build & Flash](#-build--flash)
- [Zigbee2MQTT Setup](#-zigbee2mqtt-setup)
- [What’s Different in This Fork](#-whats-different-in-this-fork)
- [Troubleshooting](#-troubleshooting)
- [Roadmap](#-roadmap)
- [Credits](#-credits)
- [License](#-license)

---

## 🧩 Hardware

| Part | Notes |
|------|------|
| **Seeed Studio XIAO ESP32-C6** | Zigbee-capable ESP32-C6 board |
| **Hi-Link LD2410 / LD2410C** | UART mmWave presence sensor |
| USB-C Cable | For flashing and logs |
| Jumper wires | UART + power |

---

## 🔌 Wiring

> ⚠️ **Important:** verify UART pins in the source code/config you are using.  
> The project works when LD2410 logs show DETECTED/CLEAR transitions.

Typical wiring:

- **LD2410 TX → XIAO RX (UART)**
- **LD2410 RX → XIAO TX (UART)**
- **LD2410 VCC → 5V**
- **LD2410 GND → GND**

---

## 🧱 Build & Flash

### Requirements
- **ESP-IDF** (recommended version: the same one used by the upstream project)
- USB drivers (if required on your OS)

### Build
```bash
idf.py set-target esp32c6
idf.py build
```

### Flash + Monitor
```bash
idf.py -p /dev/ttyUSB0 flash monitor
```

You should see logs similar to:
- `Moving Target -> DETECTED/CLEAR`
- `Static Target -> DETECTED/CLEAR`
- `Occupancy -> DETECTED/CLEAR`

---

## 🧠 Zigbee2MQTT Setup

### 1) Pairing
1. Put Zigbee2MQTT into **permit join**
2. Power the device
3. Wait for Z2M interview to complete

### 2) External converter (recommended)
If this repo includes an `external_converters` file, place it in your Z2M config and restart Z2M.

Example `configuration.yaml`:
```yaml
external_converters:
  - shs01-xiao.js
```

Then restart Zigbee2MQTT.

### 3) Force configure (if needed)
If the device pairs but states don’t update:
- Go to Zigbee2MQTT device page → click **Configure**
- Optionally run a re-interview

> **Tip:** Many “no updates” issues are solved by re-configure + re-interview after adding the converter.

---

## 🧪 What’s Different in This Fork

### ✅ XIAO ESP32-C6 Port
- Board-specific GPIO/UART mapping for XIAO ESP32-C6

### ✅ Reliable reporting for Occupancy
- Occupancy updates are reported immediately to the coordinator (Z2M), not only stored locally

### ⚙️ Moving/Static targets
- Exposed as **custom attributes**  
- Requires the **converter** to map them properly (and depends on how you want them represented in Z2M)

---

## 🛠️ Troubleshooting

### ✅ Occupancy works but Moving/Static don’t
This usually means **converter mapping** is missing or incorrect.

Checklist:
- [ ] External converter is loaded and Zigbee2MQTT is restarted  
- [ ] You can see endpoint/cluster data in Z2M device page  
- [ ] Z2M logs show attribute reports when you move/stand still

**Persian note:**  
اگر `occupancy` درست آپدیت می‌شود ولی `moving/static` نه، تقریباً همیشه مشکل از **کانورتر Z2M** است (نه UART یا سنسور).

---

### 🔁 Device pairs, controls work, but no state updates
- Press **Configure** in Z2M device page
- Re-interview
- If still stuck: remove the device from Z2M and pair again

---

### 📶 Weak signal / unstable mesh
- Ensure the device stays powered (router)
- Add another router nearby (smart plug/router)
- Avoid placing LD2410 too close to metal surfaces (can affect sensing)

---

## 🗺️ Roadmap

- [ ] Publish a clean Zigbee2MQTT converter with documented attribute IDs
- [ ] Add Home Assistant-friendly entities (binary_sensor for moving/static)
- [ ] Optional: OTA update support
- [ ] Document exact pin mapping for XIAO ESP32-C6

---

## 🙏 Credits

- Original firmware base and idea: **SmartHomeScene** project (zigbee-esp32 / SHS01)
- Community forks and Z2M presence work inspired parts of the converter approach

### Links (copy/paste)
```text
Upstream base:
https://github.com/SmartHomeScene/zigbee-esp32

ESP Zigbee SDK docs:
https://docs.espressif.com/projects/esp-zigbee-sdk/en/latest/esp32/introduction.html

Seeed XIAO ESP32-C6 Zigbee wiki:
https://wiki.seeedstudio.com/xiao_esp32c6_zigbee/
```

---

## 📄 License

This repository follows the **same license** as the upstream code it is derived from.  
If upstream has no license file, **do not publish/relicense** without clarifying permissions.

---

## ⭐ Support

If this project helped you:
- Star the repo
- Open an issue with logs + Z2M debug output
- Submit a PR for converter improvements

---
