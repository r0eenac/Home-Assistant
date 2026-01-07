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


```yaml
alias: Boiler_Daily_under26
description: "Smart Boiler Logic with Safety Fail-safes"
mode: restart

trigger:
  # 1. Daily Activation (Dynamic Time)
  - platform: template
    value_template: "{{ now().strftime('%H:%M') == states('sensor.boiler_trigger_time') }}"
    id: Triggered

  # 2. Hard Stop Failsafe (Time limit)
  - platform: time
    at: "18:32:00"
    id: Closer

  # 3. Overheat Protection (>60°C)
  - platform: template
    value_template: |2-
      {% set temp = states('sensor.boiler_temp_temperature') | float(0) %}
      {% set last_changed = states.sensor.boiler_temp_temperature.last_changed %}
      {# Calculation fixed to verify data is fresh (updated in the last 3 hours) #}
      {% set hours_since_update = (now() - last_changed).total_seconds() / 3600 %}
      {{ temp > 60 and hours_since_update < 3 }}
    id: Hot_Enough

condition:
  # Global Conditions
  - condition: state
    entity_id: input_select.home_travel
    state: home
  - condition: template
    value_template: "{{ now().month in [10, 11, 12, 1, 2, 3, 4] }}"

action:
  # --- Scenario 1: Daily Activation ---
  - if:
      - condition: trigger
        id: Triggered
      - condition: template
        value_template: "{{ is_state('sensor.boiler_turn', 'Yes') }}"
      # Check 'OFF' state here to ensure safety triggers are not blocked
      - condition: state
        entity_id: switch.boiler
        state: "off"
    then:
      - service: switch.turn_on
        target:
          entity_id: switch.boiler
      
      # Notifications
      - service: notify.greenapi
        data:
          title: "🔥🚿 Smart Boiler"
          message: "📢 Turned ON for {{ states('sensor.boiler_time') | int(30) }} minutes."
          target: 1234567890@g.us  # REPLACE THIS
      - service: telegram_bot.send_message
        data:
          title: "Smart Boiler"
          message: "📢 Turned ON for {{ states('sensor.boiler_time') | int(30) }} minutes."
          target: -987654321  # REPLACE THIS
      
      # TTS Announcement
      - service: media_player.volume_set
        target:
          entity_id: media_player.living_room_speaker
        data:
          volume_level: 0.7
      - service: tts.speak
        target:
          entity_id: tts.google_translate_iw_co_il
        data:
          media_player_entity_id: media_player.living_room_speaker
          message: "It was cold today, turning boiler ON for {{ states('sensor.boiler_time') | int(30) }} minutes."
      
      # Wait Loop
      - delay: "{{ (states('sensor.boiler_time') | int(30) * 60) }}"
      
      # Turn Off & Notify
      - service: switch.turn_off
        target:
          entity_id: switch.boiler
      - service: media_player.volume_set
        target:
          entity_id: media_player.living_room_speaker
        data:
          volume_level: 0.78
      - service: tts.speak
        target:
          entity_id: tts.google_translate_iw_co_il
        data:
          media_player_entity_id: media_player.living_room_speaker
          message: "Boiler is OFF, time to shower."
      - service: telegram_bot.send_message
        data:
          title: "Smart Boiler"
          message: "📢 Boiler turned OFF."
          target: -987654321  # REPLACE THIS
      - service: notify.greenapi
        data:
          title: "🔥🚿 Smart Boiler"
          message: "📢 Boiler turned OFF."
          target: 1234567890@g.us  # REPLACE THIS

  # --- Scenario 2: Failsafe (Closer) ---
  - if:
      - condition: trigger
        id: Closer
      - condition: state
        entity_id: switch.boiler
        state: "on"
    then:
      - service: switch.turn_off
        target:
          entity_id: switch.boiler
      - service: notify.greenapi
        data:
          title: "🔥🚿 Error"
          message: "Automation loop failed. Closing boiler via Failsafe."
          target: 1234567890@g.us  # REPLACE THIS

  # --- Scenario 3: Overheat Protection ---
  - if:
      - condition: trigger
        id: Hot_Enough
      - condition: state
        entity_id: switch.boiler
        state: "on"
    then:
      - service: notify.greenapi
        data:
          title: "🔥🚿 Max Temp"
          message: "Temperature reached limit (60°C). Turning OFF for safety."
          target: 1234567890@g.us  # REPLACE THIS
      - service: switch.turn_off
        target:
          entity_id: switch.boiler

```
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
