
# Home Assistant E-Ink Dashboard

A simple, battery-powered, Waveshare 2.9" e-ink display for Home Assistant, powered by ESPHome & ESP32.

## Project Overview

This project is a low-power, always-ready e-ink display for Home Assistant that runs on a Lolin D32 (ESP32) microcontroller. 
It was designed to display indoor and outdoor temperature, humidity, pressure, and weather conditions on a battery-powered device.

<<<<<<< HEAD
**Key features:**
- Temperature and humidity sensing via SHT31 (I²C)
- Battery and 5 V input voltage measurement using ADC (through a [resistor network](https://github.com/whitakerz/Battery-ESP32/blob/main/Resistor%20Network.txt)).
- Battery percentage estimation with nonlinear calibration
- Automatic day/night deep sleep control
- Home Assistant event logging for voltage and sleep cycles
- OTA updates and encrypted API communication
=======
Because existing code and enclosures did not meet my requirements, I combined multiple ESPHome projects, wrote custom configuration, 
and had a custom case designed on Fiverr.
>>>>>>> parent of 965846c (Update readme.md)

## Hardware Used

- **Display:** Waveshare 2.9" E-Ink Display (V2, R2), 296x128 px, partial refresh, SPI interface
- **Microcontroller:** Lolin D32 (ESP32-based)
- **Battery:** Single-cell LiPo, monitored via ADC
- **Case:** Custom-designed and printed via Fiverr
- **Resistors:** Voltage divider networks for battery and 5V sensing
- **Miscellaneous:** 200Ω resistor for onboard LED, JST connectors, wiring

## Wiring Diagram

```
Power & Dividers (left)                             ESP32 (center)             E-Ink Display (right)
───────────────────────────                   ────────────────────────      ─────────────────────────────
          +5V supply                            ┌──────────────────┐            Waveshare 2.9" v2-r2
              |                                 │      ESP32       │              (3.3V logic)
           [20kΩ]                               │                  │
              |                                 │  GPIO18 ──────────────── CLK  ─────────────► SCK
          ──● Node A (GPIO36 sense) ────────────┤◄─ GPIO36 (ADC 5V)|        (SPI clock)
              |                                 │                  |
              |                                 │  GPIO23 ──────────────── MOSI ─────────────► DIN
              |                                 │                  |    (SPI data out)
              |                                 │                  |
            (series link)                       │  GPIO15 ───────────────── CS   ─────────────► CS
              |                                 │                  |
          ──● Node B (GPIO39 wake) ─────────────┤◄─ GPIO39 (wakeup)|
              |                                 │                  |
           [30kΩ]                               │  GPIO17 ───────────────── DC   ─────────────► D/C
              |                                 │                  |
            GND                                 │  GPIO16 ───────────────── RST  ─────────────► RST
                                                │                  |
      4.2V (LiPo)                               │  GPIO4  ◄──────────────── BUSY ─────────────◄ BUSY
              |                                 │                  |
           [36kΩ]                               │  3V3  ─────────────────── VCC  ─────────────► VCC (3.3V)
              |                                 │  GND  ─────────────────── GND  ─────────────► GND
          ──● Node C (GPIO32 batt) ─────────────┤◄─ GPIO32 (ADC batt)
              |                                 │                  |
           [100kΩ]                              │  GPIO22 ──► LED (Awake) ──►───┐
              |                                 │                  |       (LED)|
            GND                                 └──────────────────┘            └───[200Ω] GND

Legend:
  [value]   = resistor
  ● Node A  = divider tap for 5V sense (GPIO36)
  ● Node B  = divider mid / wake input (GPIO39)
  ● Node C  = divider tap for LiPo sense (GPIO32)
  LED       = “Awake LED” driven by GPIO22 (add a series resistor if needed)
```

GPIO39 doubles as a wake pin for deep sleep. GPIO22 drives an LED to indicate awake state. Both battery and 5V input are monitored via resistor dividers.

## Power Management

The display is battery-powered and relies on ESPHome's deep sleep functionality:
- Runs for ~1 minute per wake cycle (configurable)
- Daytime sleep of ~15 minutes between updates
- Night mode enters 8-hour deep sleep after 10 PM
- ESP32 is underclocked to 80 MHz for power savings

## Display Layout

- Top-left shows indoor temperature and humidity.
- Top-right contains a battery indicator with charging status.
- Middle section displays outdoor temperature, humidity, and pressure.
- Bottom section shows the timestamp of the last update.

## Software (ESPHome)

The configuration uses ESPHome to pull data directly from Home Assistant entities. Battery voltage is displayed using a nonlinear LiPo discharge curve. 
The code includes logic for day/night cycles, custom refresh rates, and battery percentage calculations.

## Case

Since no suitable enclosure existed, I commissioned a custom design via Fiverr. The case supports the display, microcontroller, and battery while remaining easy to open for maintenance. STL files are included in the repository.

## Credits and Inspiration

- ESPHome Feature Requests #1109
- kotope/esphome_eink_dashboard
- hanspeda/esphome_homeassistant_display
- Plawasan’s E-Ink Gist

## Getting Started

1. Clone this repository:
   ```bash
   https://github.com/whitakerz/Battery-ESP32.git
   ```
2. Flash the ESPHome config:
   ```bash
   esphome run battery-08-08-25.yaml
   ```
3. Connect the device to Home Assistant.
4. Print and assemble the custom case.
