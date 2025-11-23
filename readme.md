# 🌱 ESPHome SHT31 Soil Sensor

Low-power ESP32-based soil and environmental monitoring firmware built with **ESPHome**.  
Designed for battery or solar-powered operation with intelligent sleep cycles and Home Assistant telemetry integration.

---

## 🧠 Overview

This project measures and reports environmental conditions using a **Sensirion SHT31** sensor.  
It includes advanced power management through ADC-based voltage monitoring and adaptive sleep scheduling.

**Key features:**
- Temperature and humidity sensing via SHT31 (I²C)
- Battery and 5 V input voltage measurement using ADC (through a [resistor Network](https://github.com/whitakerz/Battery-ESP32/blob/main/Resistor%20Network.txt)).
- Battery percentage estimation with nonlinear calibration
- Automatic day/night deep sleep control
- Home Assistant event logging for voltage and sleep cycles
- OTA updates and encrypted API communication

---

## ⚙️ Hardware

| Component        | Pin(s)  | Function              |
|------------------|----------|----------------------|
| SHT31 Sensor     | 21 / 22  | I²C SDA / SCL        |
| Battery Voltage  | 32       | ADC input (LiPo)     |
| 5 V Sense        | 36       | ADC input (USB)      |
| Wake-up Trigger  | 39       | Deep sleep wake-up   |

**Board:** ESP32 Devkit  
**Framework:** Arduino  
**CPU Frequency:** 80 MHz  

---

## 💤 Power Management

The firmware uses **deep sleep** to conserve energy.  
Wake periods last about **1 minute**, allowing sensor readings and network updates before returning to sleep.

- **Daytime sleep:** 10 minutes  
- **Nighttime sleep:** 8 hours  
- Automatically decides based on Home Assistant’s reported time  
- Sends a telemetry event (`esphome.battery_sleep`) with:
  - Reason (`day` or `night`)
  - Duration
  - Battery voltage
  - Timestamp

When external 5 V power is lost, the device enters deep sleep immediately to protect the battery.

---

## 🌡️ Sensor Functions

- **SHT31** provides temperature and humidity readings.
- **ADC inputs** measure:
  - 5 V presence
  - Battery voltage
- Battery percentage is calculated using a **nonlinear curve** approximating LiPo discharge behavior.
- Readings are smoothed using a sliding average to minimize noise.

---

## 📡 Connectivity

- Static Wi-Fi configuration with fallback AP for recovery  
- Encrypted ESPHome API for Home Assistant integration  
- OTA updates enabled with password protection  
- Real-time logging via the ESPHome `logger` component (debug level)

---

## 🧩 Home Assistant Integration

The device integrates directly with Home Assistant through the native ESPHome API.  
It exposes:
- Temperature and humidity sensors  
- Battery voltage and percentage  
- Internal “Voltage Present” status  
- Custom telemetry events for each sleep cycle  

These events can be used for automation or historical analysis of battery health and uptime.

---

## 🔧 Customization

You can modify the following:
- **Sleep intervals** to optimize battery performance  
- **ADC multipliers** to match your resistor divider ratios  
- **Nighttime cutoff hour** (default: after 21:00)  
- **Static IP configuration** for Wi-Fi stability  

All parameters are defined through YAML substitutions and can be adjusted without altering logic flow.

---

## 🪴 Typical Use Case

Ideal for:
- Remote soil and environment monitoring  
- Solar-powered IoT setups  
- Battery-powered outdoor sensors  
- Long-term deployments with minimal maintenance  

---

## 🧠 Dependencies

- ESPHome ≥ 2023.11  
- Home Assistant (optional for telemetry)  
- SHT31 I²C sensor  

---

## 📜 License

This project is licensed under the **GNU General Public License v3.0 (GPL-3.0)**.  
See the [LICENSE](LICENSE) file for full text.
