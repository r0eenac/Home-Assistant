# Smart Radiator Control - Hardware & Logic Architecture

This document outlines the hardware requirements and the core logic behind the radiator automation project. It explains why a standard radiator thermostat is insufficient and how this solution optimizes both comfort and energy costs.

## 🛠 Hardware Requirements (Bill of Materials)

To implement this automation, the following components are required:

1.  **Shelly 1 PM:**
    * **Function:** Smart Relay to control power (On/Off).
    * **Reasoning:** The "PM" (Power Metering) model is crucial. It allows for monitoring actual energy consumption, providing visibility into electricity costs.
    * <img width="184" height="184" alt="image" src="https://github.com/user-attachments/assets/0b48fcee-a6a3-4b6f-abba-f7613b6d5021" />

2.  **External Temperature Sensor:**
    * **Function:** Measures the actual room temperature.
    * **Placement:** Must be placed away from the radiator (e.g., near the bed) to get a true reading of the room's climate.
    * <img width="184" height="184" alt="image" src="https://github.com/user-attachments/assets/c8191cdf-ab32-45e8-aa13-af95d459f7a2" />

3.  **Home Assistant Helpers:**
    * Two **Threshold** (Binary Sensor) helpers are required in the software configuration (see Logic section below).

---

## 💡 The Problem: The "Thermostat Paradox"

Standard oil-filled radiators come with a built-in mechanical thermostat. After extensive testing, this mechanism was found to be highly inefficient for two main reasons:

1.  **False Readings (Micro-climate):** The built-in thermostat is attached to the heating element. It measures the temperature of the radiator itself, not the room. It often "thinks" the room is hot because the metal fins are hot, causing it to turn off prematurely while the room remains cold.
2.  **Short Cycling & Energy Waste:** Due to the false readings, the radiator enters a cycle of turning on and off every ~30 minutes. This "Short Cycling" is inefficient, causing unnecessary wear on the device and resulting in **significant waste of money** on electricity without actually heating the room effectively.

---
