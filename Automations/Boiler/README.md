# 🚿 Smart Winter Boiler Automation

[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Automation-blue?style=flat&logo=home-assistant)](https://www.home-assistant.io/)
[![Energy Saver](https://img.shields.io/badge/Utility-Energy%20Saver-green?style=flat&logo=leaf)](https://github.com/topics/energy)
[![Safety](https://img.shields.io/badge/Safety-Overheat%20Protection-red?style=flat&logo=security)](https://github.com/topics/security)

This advanced automation manages the water heater (Boiler) operation during winter months. It uses dynamic scheduling, temperature monitoring, and multiple safety failsafes to ensure hot water availability while saving energy and preventing overheating.

---

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/331ab89a-1b7d-46ed-bf19-829c41c09839" />


<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/23d51740-a294-477a-88dc-ef28920506d3" />


## ⚡ Key Features

1.  **Dynamic Scheduling:** Starts automatically based on a calculated time (`sensor.boiler_trigger_time`).
2.  **Smart Conditions:** Only runs during winter months (Oct-Apr), when the family is home (and not on vacation), and if the boiler is currently OFF.
3.  **Safety Cutoff (Overheat Protection):** Immediately turns off the boiler if the temperature exceeds **60°C** (logic checks for fresh data within the last 3 hours).
4.  **Hard Failsafe:** Forces a shutdown at **18:32** daily to prevent the boiler from running overnight if the main loop fails.
5.  **Multi-Channel Notifications:** Sends updates via WhatsApp (GreenAPI), Telegram, and local TTS (Text-to-Speech) on Google Home speakers.

## 📋 Prerequisites

This automation relies on the following custom entities:
* `switch.boiler`: The smart switch controlling the heater.
* `sensor.boiler_trigger_time`: Calculates the optimal start time.
* `sensor.boiler_time`: Determines the run duration (in minutes).
* `sensor.boiler_temp_temperature`: Temperature sensor (e.g., Shelly/Sonoff/Tuya).
* `notify.greenapi`: WhatsApp integration.

## 🛠️ Automation Code (YAML)

> **Note:** Sensitive IDs (Telegram Chat ID, WhatsApp Group ID) have been replaced with placeholders. Please update them with your own.

## 🛠️ Required templates 
This block is the boiler trigger time which calculate when should the boiler turned on, in order to achieve hot water at 1830 


```yaml
sensor:
  - platform: template
    sensors:
      # 1. Calculate Start Time (Target: 18:30 minus Duration)
      boiler_trigger_time:
        friendly_name: "Boiler Trigger Time"
        value_template: >
          {% set base_time = 18 * 60 + 30 %}
          {% set offset = states('sensor.boiler_time') | int(0) %}
          {% set target_minutes = (base_time - offset) %}
          {{ '{:02}:{:02}'.format(target_minutes // 60, target_minutes % 60) }}

      # 2. Calculate Duration (Logic based on daily Max/Median Temp)
      boiler_time:
        friendly_name: "Calculated Boiler Duration"
        unit_of_measurement: "min"
        value_template: >-
          {% set max_t = states('sensor.max_temp_today') | float(0) %}
          {% set med_t = states('sensor.median_temp_today') | float(0) %}

          {% if max_t < 19 and med_t < 15 %}
            90
          {% elif max_t < 21 and med_t < 17 %}
            90
          {% elif max_t < 23 and med_t < 19 %}
            60
          {% elif max_t < 26 and med_t < 24 %}
            50
          {% elif max_t < 27 and med_t < 24 %}
            40
          {% else %}
            0
          {% endif %}
  ```

and the auto flag bool -
```yaml
alias: "System: Reset Boiler Flag"
description: ""
triggers:
  - trigger: state
    entity_id: switch.boiler
    to: "off"
actions:
  - action: input_boolean.turn_off
    target:
      entity_id: input_boolean.boiler_auto_mode
```
