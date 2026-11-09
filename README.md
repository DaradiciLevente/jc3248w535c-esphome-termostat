# jc3248w535c-esphome-termostat

Advanced DIY Smart Thermostat built with JC3248W535C ESP32-S3 display board, ESPHome, and Home Assistant.
---

> [!WARNING]
>  **DISCLAIMER:** 
> * This project is a DIY (Do It Yourself) hobby endeavor. It involves working with sensitive electronic components, high and dangerous electrical voltages (230V), and gas heating systems.
> 
> * This project is provided for educational and experimental purposes only and is distributed "AS IS", without warranty of any kind, express or implied.
> * The author does not recommend, endorse, or warrant the use of this project in real-world, production, or home installations.
> * The author assumes NO legal responsibility, liability, or obligation for any direct or indirect damages, property loss, personal injury, or accidents resulting from the use, misuse, or implementation of this project.
> * By downloading, cloning, or using any part of this code or documentation, you explicitly acknowledge that you are solely responsible for compliance with local electrical and safety regulations, and you assume 100% of the risk and legal liability.
---
## 🛠️ Prerequisites & Environment

To successfully compile and flash this firmware to your JC3248W535 display board, the project has been tested and verified with the following environment:

* **OS:** Cross-platform (Windows, Linux, or macOS via Terminal)
* **Python:** `3.11.6+`
* **ESPHome:** `2025.11.0+`
* **Framework:** `ESP-IDF` (automatically configured via platformio_options in the YAML file)
---
  ## 🌟 Core Architecture & Home Assistant Integration

This thermostat is designed with a hybrid architecture that ensures absolute reliability and flexibility:

* **Standalone Operation (No HA Required):** The ESP32-S3 board runs its own internal control loop via ESPHome. It reads the local DS18B20 temperature sensor, evaluates hysteresis, controls the heating relay directly, and allows full manual control (temperature adjustment, calibration, hysteresis, vacation mode, and screen brightness) directly from the touch display—even if your Wi-Fi or Home Assistant server goes offline.
* **Home Assistant Integration (Enhanced Features):** When connected to Home Assistant via the native API, the thermostat unlocks extended capabilities:
  * Synchronization of target temperatures and vacation modes.
  * Integration of outdoor weather data and condition icons (`weather.forecast_home`).
  * Remote monitoring and automations via Home Assistant dashboards.
---
## 🔌 Wiring & Hardware Connections

The JC3248W535C display board integrates an ESP32-S3 chip. Below are the physical pin allocations and peripheral connections used in this project:

| Component | Pin / Interface | Description |
| :--- | :--- | :--- |
| **Display Board** | `JC3248W535C` | 3.5" IPS Capacitive Touch Display (MIPI SPI) |
| **Touch Controller** | `AXS15231` | Integrated capacitive touch interface |
| **Backlight PWM** | `GPIO 1` | Backlight brightness control via LEDC |
| **DS18B20 Sensor** | `GPIO 17` | 1-Wire data line for room temperature measurement (requires 4.7kΩ pull-up resistor) |
| **Heating Relay** | `GPIO 18` | Digital output to trigger the boiler relay module |
| **I2C Bus** | SDA: `GPIO 4`, SCL: `GPIO 8` | Reserved for optional sensors (e.g., SHT45 / BME280) |
| **SPI Bus (Display)** | CLK: `GPIO 47`, Data: `[21, 48, 40, 39]` | Quad SPI interface for high-speed display rendering |

> [!CAUTION]
> **ELECTRICAL HAZARD:** 
> * Connecting the relay output to your heating system involves working with **230V mains voltage**. 
> * Always disconnect both the central heating boiler (gas/electric heating system) and the thermostat power supply from the mains before wiring any physical components. 
> * If you are unsure or lack qualifications, always consult a certified electrician.
---
## 🚀 Installation & Setup Guide

Follow these steps to set up, configure, and flash the firmware onto your JC3248W535 display board using ESPHome.

### Step 1: Clone or Download the Repository
Clone this repository or download the source files into your local ESPHome configuration directory:
```bash
git clone [https://github.com/YourUsername/jc3248w535c-esphome-termostat.git](https://github.com/YourUsername/jc3248w535c-esphome-termostat.git)
```
---
