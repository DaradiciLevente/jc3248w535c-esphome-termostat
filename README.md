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
