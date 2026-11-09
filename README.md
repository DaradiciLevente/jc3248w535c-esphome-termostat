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

### Step 2: Configure secrets.yaml
In your main ESPHome configuration directory (where your secrets file resides), ensure you have defined the necessary credentials. Create or update your secrets.yaml file with the following entries:
```bash
wifi_ssid: "Your_WiFi_SSID"
wifi_password: "Your_WiFi_Password"
ap_ssid: "Thermostat-Fallback-AP"
ap_password: "Fallback_Password"
api_password: "Your_ESPHome_API_Password"
ota_password: "Your_OTA_Password"
api_key: "Your_Encryption_Key_Base64"
```
> [!TIP]
> **Generating Encryption Keys:**
> If you need to generate a secure 32-character base64 encryption key for the `api_key` in your `secrets.yaml`, you can run the following command in your terminal using ESPHome:
> ```bash
> python -c "import base64, os; print(base64.b64encode(os.urandom(32)).decode())"
> ```
> This will output a secure key that you can copy and paste directly into your secrets file.
> 
### Step 3: Update Sensor Entities (Substitutions)
Open the YAML configuration file and update the top substitution variables to match your Home Assistant entity IDs:
```bash
substitutions:
  sensor_temp_exterior: "sensor.your_outdoor_temperature_sensor"
  sensor_stare_vreme: "weather.forecast_your_home"
```

### Step 4: Compile and Flash
Connect your ESP32-S3 display board to your computer via USB and run the compilation command using ESPHome CLI:
```bash
esphome run termostat-esphome.yaml
```
##### (Note: For subsequent updates once the device is mounted on the wall and connected to your local network, ESPHome will automatically perform Over-The-Air (OTA) updates.)
---
## 📱 Features & Touch Interface Guide

The thermostat features a modern, responsive 3.5" IPS capacitive touch interface powered by the `AXS15231` controller and built using ESPHome's custom display rendering engine.

### 1. Main Dashboard
* **Real-time Clock & Date:** Synchronized automatically via SNTP (configured for your local timezone).
* **Network & Weather Status:** Live Wi-Fi signal strength indicator and outdoor temperature/weather condition watermark (synced from Home Assistant).
* **Current Room Temperature:** Large display showing the precise temperature measured by the local DS18B20 sensor (with customizable temperature calibration offset).
* **Target Temperature Box:** Clear indication of the target setpoint (or holiday setpoint).
* **Heating State Indicator:** Visual status showing **"ÎNCĂLZIRE"** (Heating active with a smooth animated flame effect), **"REPAUS"** (Idle), or **"OPRIT"** (Off mode).
* **Quick Controls:** Direct `-` and `+` touch buttons at the bottom to adjust the target temperature by 0.5°C increments.
* **Settings Access:** Tapping the gear icon in the top-right corner opens the settings menu with a smooth transition animation.

### 2. Settings Menu
Tapping the gear icon gives you direct access to configure critical parameters locally on the device (backed up to flash preferences):
* **Unit of Measurement:** Toggle between Celsius (°C) and Fahrenheit (°F).
* **Temperature Calibration (Calibrare):** Fine-tune sensor offsets from -5.0°C to +5.0°C.
* **Hysteresis:** Adjust the heating differential (from 0.1°C to 2.0°C) to prevent short-cycling of the boiler.
* **Minimum On/Off Time (Timp minim):** Set protection delays (0 to 600 seconds).
* **Vacation Mode (Mod Vacanță):** Toggle vacation mode **ON/OFF** to quickly switch between normal operation and eco mode.
* **Vacation Temperature (Temp. Vacanță):** Set the dedicated eco-temperature target used when vacation mode is active (adjustable from 5.0°C to 20.0°C).
* **Display Brightness (Luminozitate):** Adjust backlight PWM level from 10% to 100%.
* **Outdoor Temperature Display:** Enable or disable the outdoor temperature and weather icon overlay on the main screen.
---
## 🛠️ Troubleshooting

If you encounter issues during setup, compilation, or runtime, check the common solutions below:

### 1. DS18B20 Temperature Sensor reads `85.0°C` or `--.-°C`
* **Cause:** A reading of `85.0°C` is the power-on reset default value of the DS18B20 chip, indicating a communication failure or incorrect wiring. 
* **Solution:** 
  * Check the wiring on `GPIO 17`.
  * Ensure a **4.7kΩ pull-up resistor** is properly connected between the Data line and VCC (3.3V).
  * Verify that the sensor is receiving stable 3.3V power.
  * The firmware includes built-in filters to automatically discard `85.0°C` and out-of-range values, but proper wiring is essential.

### 2. Display Remains Black or Touch Is Unresponsive
* **Cause:** Incorrect pin definitions or SPI/MIPI configuration mismatch.
* **Solution:**
  * Verify that your hardware board is strictly the **JC3248W535C** (ESP32-S3 model with MIPI SPI display and `AXS15231` touch controller).
  * Ensure the SPI pins (`CLK: 47`, Data: `[21, 48, 40, 39]`) and I2C pins (`SDA: 4`, `SCL: 8`) match your physical PCB revision.
  * Check the serial logs via USB (`esphome logs termostat-esphome.yaml`) to inspect potential boot loops or driver errors.

### 3. Wi-Fi Connection Failures on Boot
* **Cause:** Incorrect credentials in `secrets.yaml` or weak signal strength.
* **Solution:**
  * Double-check your `wifi_ssid` and `wifi_password` in `secrets.yaml`.
  * If the device cannot reach your primary network, it will start its own fallback Access Point (`ap_ssid`) allowing you to connect directly and check logs or update credentials via OTA.
---
## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](../../issues) if you want to contribute.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---
<div align="center">

> *"Built with passion for makers, DIY enthusiasts, and smart home tinkerers."*
> 
> **Crafted by Levente Daradici** 🚀

</div>
---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` file for more information.
