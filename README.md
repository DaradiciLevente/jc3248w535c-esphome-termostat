<div align="center">

# 🔥 ESP32-S3 Touch Thermostat

### Standalone ESPHome thermostat & boiler controller with a 3.5" touch display

[![ESPHome](https://img.shields.io/badge/ESPHome-2025.11.0%2B-000000?logo=esphome&logoColor=white)](https://esphome.io)
[![Framework](https://img.shields.io/badge/Framework-ESP--IDF-E7352C?logo=espressif&logoColor=white)](https://github.com/espressif/esp-idf)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-optional-41BDF5?logo=homeassistant&logoColor=white)](https://www.home-assistant.io)
[![Python](https://img.shields.io/badge/Python-3.11.6%2B-3776AB?logo=python&logoColor=white)](https://www.python.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Work%20in%20progress-orange)](#-roadmap)

<br>

<a href="https://www.youtube.com/watch?v=7YacdU6UZSM">
  <img src="images/main2.jpg" alt="Thermostat main dashboard" width="320">
</a>

**[▶ Watch the demo on YouTube](https://www.youtube.com/watch?v=7YacdU6UZSM)**

</div>

---

> [!WARNING]
> **Mains voltage & gas heating. Read the [disclaimer](#️-disclaimer--safety) before building anything.**

## 📑 Table of Contents

- [✨ Highlights](#-highlights)
- [🏗️ Architecture](#️-architecture)
- [📸 Screenshots](#-screenshots)
- [📱 Touch Interface Guide](#-touch-interface-guide)
- [🧰 Hardware & Wiring](#-hardware--wiring)
- [🚀 Installation](#-installation)
- [🩺 Troubleshooting](#-troubleshooting)
- [🗺️ Roadmap](#️-roadmap)
- [🤝 Contributing](#-contributing)
- [⚠️ Disclaimer & Safety](#️-disclaimer--safety)
- [📄 License](#-license)

---

## ✨ Highlights

| | Feature | Details |
| :-: | :--- | :--- |
| 🧠 | **Local control loop** | Reads the DS18B20, evaluates hysteresis and drives the relay on the device itself. No cloud, no server needed. |
| 📡 | **Works offline** | Wi-Fi or Home Assistant down? The thermostat keeps heating and stays fully controllable from the screen. |
| 🏠 | **Home Assistant ready** | Native API for target temperature sync, vacation mode, outdoor weather and dashboards. |
| 🖐️ | **Full touch UI** | Dashboard + settings menu with animated transitions and an animated flame while heating. |
| 🛡️ | **Boiler protection** | Configurable hysteresis and minimum ON/OFF times to prevent short-cycling. |
| 💾 | **Persistent settings** | All parameters are stored in flash and survive reboots and power loss. |
| 🌴 | **Vacation mode** | One tap to switch to a dedicated eco temperature. |
| 🔄 | **OTA updates** | Update wirelessly once the device is mounted on the wall. |

---

## 🏗️ Architecture

The thermostat uses a **hybrid design**: the ESP32-S3 is the brain, and Home Assistant is an optional extra layer.

```mermaid
flowchart LR
    S["🌡️ DS18B20<br/>GPIO 17"] --> C
    subgraph ESP["ESP32-S3 · JC3248W535C"]
        C["Control loop<br/>(hysteresis + min on/off)"]
        T["🖐️ Touch UI<br/>AXS15231"]
        T <--> C
    end
    C --> R["🔌 Relay<br/>GPIO 18"]
    R --> B["🔥 Boiler"]
    C <-. "Native API (optional)" .-> HA["🏠 Home Assistant"]
    HA -. "outdoor temp + weather" .-> C
```

**Standalone (no HA required)**
- Local temperature reading, hysteresis evaluation and direct relay control
- Full manual control from the display: target temperature, calibration, hysteresis, vacation mode, brightness

**With Home Assistant (enhanced)**
- Synchronized target temperatures and vacation mode
- Outdoor temperature and weather condition icon (`weather.forecast_home`)
- Remote monitoring and automations from your dashboards

---

## 📸 Screenshots

| Main Dashboard | Settings Menu |
| :---: | :---: |
| <img src="images/main2.jpg" alt="Main Dashboard" width="300"> | <img src="images/settings.jpg" alt="Settings Menu" width="300"> |

<div align="center">

[![Watch the video](https://img.youtube.com/vi/7YacdU6UZSM/0.jpg)](https://www.youtube.com/watch?v=7YacdU6UZSM)

*Click the thumbnail to watch touch navigation and the animated heating flame.*

</div>

---

## 📱 Touch Interface Guide

The UI runs on a 3.5" IPS capacitive touch screen (`AXS15231` controller) and is rendered with ESPHome's display engine.

### Main Dashboard

| Element | Description |
| :--- | :--- |
| 🕐 **Clock & date** | Synchronized via SNTP for your local timezone |
| 📶 **Network & weather** | Wi-Fi signal strength plus outdoor temperature and weather icon (from Home Assistant) |
| 🌡️ **Room temperature** | Large readout from the DS18B20, with a calibration offset |
| 🎯 **Target temperature** | Current setpoint (or the vacation setpoint) |
| 🔥 **Heating state** | `ÎNCĂLZIRE` (heating, animated flame) · `REPAUS` (idle) · `OPRIT` (off) |
| ➕➖ **Quick controls** | Adjust the target in **0.5 °C** steps |
| ⚙️ **Settings** | Gear icon (top-right) opens the settings menu |

### Settings Menu

All values are saved to flash preferences.

| Setting | Range / Options |
| :--- | :--- |
| **Unit** | °C / °F |
| **Calibrare** (calibration) | −5.0 °C … +5.0 °C |
| **Hysteresis** | 0.1 °C … 2.0 °C |
| **Timp minim** (min. ON/OFF time) | 0 … 600 s |
| **Mod Vacanță** (vacation mode) | ON / OFF |
| **Temp. Vacanță** (vacation temp.) | 5.0 °C … 20.0 °C |
| **Luminozitate** (brightness) | 10 % … 100 % |
| **Outdoor temperature overlay** | Show / hide on the main screen |

---

## 🧰 Hardware & Wiring

### Bill of materials

| Part | Notes |
| :--- | :--- |
| **JC3248W535C** | ESP32-S3 board with 3.5" IPS capacitive touch display |
| **DS18B20** | 1-Wire temperature sensor |
| **4.7 kΩ resistor** | Pull-up between DATA and 3.3 V |
| **Relay module** | Rated for your boiler's control circuit |
| **5 V power supply** | Stable supply for the board |

### Pin allocation

| Component | Pin / Interface | Description |
| :--- | :--- | :--- |
| **Display** | `JC3248W535C` | 3.5" IPS capacitive touch display (QSPI) |
| **Touch controller** | `AXS15231` | Integrated capacitive touch |
| **Backlight PWM** | `GPIO 1` | Brightness control via LEDC |
| **DS18B20** | `GPIO 17` | 1-Wire data (needs 4.7 kΩ pull-up) |
| **Heating relay** | `GPIO 18` | Digital output to the boiler relay module |
| **I²C bus** | SDA `GPIO 4` · SCL `GPIO 8` | Optional sensors (e.g. SHT45 / BME280) |
| **SPI bus (display)** | CLK `GPIO 47` · Data `[21, 48, 40, 39]` | Quad SPI for fast rendering |

### DS18B20 wiring

```text
 3.3V ──┬───────────── VCC (DS18B20)
        │
      [4.7kΩ]
        │
 GPIO17 ┴───────────── DATA (DS18B20)

 GND ────────────────── GND (DS18B20)
```

> [!CAUTION]
> **ELECTRICAL HAZARD: 230 V mains**
> - Connecting the relay to your heating system means working with mains voltage.
> - Disconnect **both** the boiler and the thermostat power supply from the mains before touching any wiring.
> - If you are unsure or unqualified, **consult a certified electrician**.

---

## 🚀 Installation

**Tested environment**

| Tool | Version |
| :--- | :--- |
| OS | Windows, Linux or macOS (terminal) |
| Python | `3.11.6+` |
| ESPHome | `2025.11.0+` |
| Framework | `ESP-IDF` (configured automatically via `platformio_options` in the YAML) |

### 1️⃣ Clone the repository

```bash
git clone https://github.com/DaradiciLevente/jc3248w535c-esphome-termostat.git
cd jc3248w535c-esphome-termostat
```

### 2️⃣ Configure `secrets.yaml`

Create or update `secrets.yaml` in your ESPHome configuration directory:

```yaml
wifi_ssid: "Your_WiFi_SSID"
wifi_password: "Your_WiFi_Password"
ap_ssid: "Thermostat-Fallback-AP"
ap_password: "Fallback_Password"
api_password: "Your_ESPHome_API_Password"
ota_password: "Your_OTA_Password"
api_key: "Your_Encryption_Key_Base64"
```

> [!TIP]
> **Generate a valid API encryption key** (32 random bytes, base64-encoded):
> ```bash
> python -c "import base64, os; print(base64.b64encode(os.urandom(32)).decode())"
> ```

### 3️⃣ Set your Home Assistant entities

Edit the `substitutions` block at the top of the YAML file:

```yaml
substitutions:
  sensor_temp_exterior: "sensor.your_outdoor_temperature_sensor"
  sensor_stare_vreme: "weather.forecast_your_home"
```

### 4️⃣ Compile & flash

Connect the board over USB and run:

```bash
esphome run termostat-esphome.yaml
```

> [!NOTE]
> After the first flash, once the device is on your network, later updates are done **over the air (OTA)**.

---

## 🩺 Troubleshooting

<details>
<summary><b>🌡️ DS18B20 reads <code>85.0 °C</code> or <code>--.-</code></b></summary>

<br>

**Cause:** `85.0 °C` is the chip's power-on default and indicates a communication or wiring problem.

**Fix:**
- Check the wiring on `GPIO 17`.
- Make sure a **4.7 kΩ pull-up** connects DATA to 3.3 V.
- Verify the sensor gets a stable 3.3 V supply.
- The firmware filters out `85.0 °C` and out-of-range values, but correct wiring is still essential.

</details>

<details>
<summary><b>🖥️ Display stays black or touch does not respond</b></summary>

<br>

**Cause:** wrong pin definitions or an SPI/display configuration mismatch.

**Fix:**
- Confirm your board is exactly the **JC3248W535C** (ESP32-S3, QSPI display, `AXS15231` touch).
- Check the SPI pins (`CLK: 47`, Data: `[21, 48, 40, 39]`) and I²C pins (`SDA: 4`, `SCL: 8`) against your PCB revision.
- Inspect the serial logs for boot loops or driver errors:
  ```bash
  esphome logs termostat-esphome.yaml
  ```

</details>

<details>
<summary><b>📶 Wi-Fi connection fails on boot</b></summary>

<br>

**Cause:** wrong credentials in `secrets.yaml` or a weak signal.

**Fix:**
- Double-check `wifi_ssid` and `wifi_password`.
- If the main network is unreachable, the device starts its fallback access point (`ap_ssid`). Connect to it to check logs or update credentials via OTA.

</details>

---

## 🗺️ Roadmap

This project is under active development. Documentation and features are still evolving.

- [x] Local control loop with hysteresis and min ON/OFF time
- [x] Touch UI with settings menu and animated flame
- [x] Vacation mode and persistent settings
- [x] Home Assistant integration (weather + sync)
- [ ] Optional I²C sensors (SHT45 / BME280)
- [ ] Weekly schedule
- [ ] More languages for the UI

*Edit this list to match your real plans.*

---

## 🤝 Contributing

Contributions, issues and feature requests are welcome! Check the [issues page](../../issues).

1. Fork the project
2. Create your feature branch: `git checkout -b feature/AmazingFeature`
3. Commit your changes: `git commit -m 'Add some AmazingFeature'`
4. Push to the branch: `git push origin feature/AmazingFeature`
5. Open a Pull Request

---

## ⚠️ Disclaimer & Safety

> [!WARNING]
> - This is a **DIY hobby project**. It involves sensitive electronics, **dangerous 230 V mains voltage** and **gas heating systems**.
> - It is provided for **educational and experimental purposes only**, distributed **"AS IS"**, without warranty of any kind, express or implied.
> - The author does **not** recommend, endorse or warrant its use in real-world, production or home installations.
> - The author assumes **no legal responsibility or liability** for any direct or indirect damage, property loss, personal injury or accident resulting from the use, misuse or implementation of this project.
> - By downloading, cloning or using any part of this code or documentation, you acknowledge that you are **solely responsible** for compliance with local electrical and safety regulations, and you assume **100% of the risk and liability**.

---

## 📄 License

Distributed under the **MIT License**. See [`LICENSE`](LICENSE) for details.

---

<div align="center">

*Built with passion for makers, DIY enthusiasts and smart home tinkerers.*

**Crafted by [Levente Daradici](https://github.com/DaradiciLevente)** 🚀

⭐ If this project helped you, consider giving it a star!

</div>
