# Automation Logic & Workflow

This document explains the software logic behind the Smart Radiator automation, specifically why we bypass the internal thermostat and use Home Assistant helpers.

## 🧠 The Logic: Bypassing the "Thermostat Paradox"

### The Problem
Standard oil-filled radiators use a mechanical thermostat attached to the heating element. This creates a "Micro-climate" issue where the thermostat measures the radiator's heat rather than the room temperature. This leads to **Short Cycling**—the radiator turns on and off frequently, wasting money without effectively heating the room.

### The Solution
We set the radiator's internal thermostat to **MAX** (always on) and control the power externally using a Shelly 1PM based on a remote temperature sensor.

---

## ⚙️ The Helpers Strategy

To manage the automation reliable, we do not use simple numeric triggers. Instead, we use two **Binary Sensor Helpers (Thresholds)**:
1.  **Low/Cold Helper** (e.g., turns ON below 19°C)
2.  **High/Hot Helper** (e.g., turns ON above 23°C)

   <img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/074b6045-9a5d-4c82-837e-0f0499a786bc" />
   <img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/7e766691-57c6-4358-81bd-8e01c8eb951b" />
   <img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/5021da11-ca69-4edb-a24a-52dcaf374087" />
   <img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/08bcb4cd-fcc5-480a-ba52-795356808246" />

   


### Why use Helpers?

#### 1. Creating Hysteresis (Deadband)
Direct numeric triggers can cause the device to toggle rapidly (e.g., ON at 19.9°, OFF at 20.0°).
* **The Fix:** By using two separate thresholds, we create a wide operating range. The radiator turns **ON** only when deep in the cold zone and **OFF** only when fully warm. This ensures long, efficient heating cycles.

#### 2. Solving the "Restart Bug"
In Home Assistant, a standard `Numeric State` trigger only fires when the value **crosses** the threshold.
* **The Risk:** If Home Assistant restarts (e.g., after a power outage) and the room is *already* freezing (e.g., 17°), a standard trigger will **not** fire because the "crossing" event happened while the system was down.
* **The Fix:** Binary Helpers maintain their state (`On` or `Off`). When Home Assistant boots up, it immediately sees that the `Low Temp` helper is `Active` and triggers the heating instantly, ensuring reliability.
### Room temperature -
<img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/be0654cb-40cd-478c-89be-4cd40ab6c09e" />

### Radiator usage -
<img width="495" height="228" alt="image" src="https://github.com/user-attachments/assets/28b4590f-1478-465b-9e42-af3c04f058a6" />

only worked 3 times during the night , instead of a whole night 

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/2d8d12a2-247d-4cd4-bfa0-3dd919c2a33d" />

