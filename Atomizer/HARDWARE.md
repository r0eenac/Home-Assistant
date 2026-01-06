# 🛠️ DIY Hardware Modification Guide

> **Project Goal:** Converting a proprietary "dumb" aroma diffuser (Miyoscent) into a locally controlled IoT device using Sonoff SV.

---

## ⚠️ LEGAL DISCLAIMER & WARNING

**PLEASE READ CAREFULLY BEFORE PROCEEDING:**

* **HIGH VOLTAGE RISK:** While this specific guide focuses on 5V USB power, working with any electronics involves risks. If you choose to use alternative power supplies or modify mains-powered devices, you risk electric shock, fire, and death.
* **NO WARRANTY:** Modifications described herein verify void the manufacturer's warranty.
* **LIMITATION OF LIABILITY:** This guide is provided "AS IS" without warranty of any kind. The author assumes **NO RESPONSIBILITY** for any damage to equipment, personal injury, or fire resulting from the use or misuse of this information. **You perform these modifications entirely at your own risk.**

---

## 📋 Bill of Materials (BOM)

| Component | Description | Role |
| :--- | :--- | :--- |
| **Miyoscent Diffuser** | Standard commercial unit | The "Donor" (Shell & Fluid tank). |
| **Sonoff SV** | Safe Voltage WiFi Relay | The new MCU/Controller (5V-24V DC). |
| **Atomizer Driver** | Ultrasonic Transducer Plate | Generates the mist. |
| **Power Supply** | 5V DC Source (USB) | Powering the Sonoff & Driver. |

---

## 📸 Components Overview

| Component | Visual Reference |
| :--- | :--- |
| **1. Miyoscent Diffuser**<br>*(The Donor Device)* | <img src="https://github.com/user-attachments/assets/054bd433-2b92-4366-8cfc-c5f7e016db98" width="150" /> |
| **2. Atomizer Maker**<br>*(20mm Ultrasonic Plate)* | <img src="https://github.com/user-attachments/assets/5c7af0f7-fd53-4f2e-b34f-6b3f527fa6d8" width="150" /> |
| **3. Sonoff SV**<br>*(The Smart Controller)* | <img src="https://github.com/user-attachments/assets/5eba0551-71f3-45b3-a0cb-9d43c6e16f3a" width="150" /> |

### ⚡ Critical Technical Note: Sonoff SV
The **Sonoff SV (Safe Voltage)** is designed for **DC 5V-24V input only**.
* **DO NOT** connect 110V/220V AC directly to the input terminals. This will destroy the device.
* **Jumper Setting:** For this 5V project, ensure the input jumper is inserted correctly to bridge the power supply to the relay.

---

## 🔧 The Build Process

### 1. Teardown & "Lobotomy"
The original proprietary logic board (PCB) was completely removed from the Miyoscent casing. This removes the manual buttons and the original limited firmware, leaving only the plastic shell and the tank assembly.

### 2. The Controller: Sonoff SV
We selected the Sonoff SV because it operates on low DC voltage and provides a clean, isolate-able relay.

* **Voltage Modification:** The Sonoff SV is powered by a standard **5V** USB power supply.
* **Wiring:**
    * **Input:** 5V DC (from USB breakout).
    * **Output (Relay):** The relay output is wired to trigger the **Atomizer Driver Plate**.

### 3. Integration
The driver plate and the Sonoff SV were mounted inside the original casing. The ultrasonic disc was sealed against the liquid tank using the original gasket to prevent leaks.

---

## ⚡ Logic Explanation

### Why Time-Based Control?
A common question is why we control the **duration** of the spray rather than the **intensity** (volume).

1.  **The Limitation:** The Sonoff SV acts as a **Binary Relay** (On/Off switch). It simply closes or opens a circuit.
2.  **Intensity Requirements:** To control mist intensity, the driver plate would require a **PWM (Pulse Width Modulation)** signal.
3.  **The Solution:** Since the Sonoff SV (in this configuration) does not output PWM:
    * **State:** Full Power (On) / Off.
    * **Dosage Control:** Achieved by limiting the *time* the device is active (e.g., 10-second bursts) via Home Assistant automation.
