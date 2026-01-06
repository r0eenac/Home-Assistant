# Home-Assistant

# 🏠 Roee's Smart Home & Homelab

![Home Assistant](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?style=for-the-badge&logo=home-assistant&logoColor=white)
![n8n](https://img.shields.io/badge/n8n-%23EA4B71.svg?style=for-the-badge&logo=n8n&logoColor=white)
![Proxmox](https://img.shields.io/badge/proxmox-%23E57000.svg?style=for-the-badge&logo=proxmox&logoColor=white)
![Ubiquiti](https://img.shields.io/badge/ubiquiti-%230559C9.svg?style=for-the-badge&logo=ubiquiti&logoColor=white)

> **"Automation should be felt, not seen."**

Welcome to my smart home configuration repository. This setup focuses on **local control**, **data privacy**, and **proactive automation** rather than just remote control. The system runs on a robust Proxmox cluster and leverages n8n for complex workflows.

---

## 🖥️ The Hardware Stack

My setup is built for reliability and performance, moving away from Raspberry Pis to enterprise-grade micro-clients.

| Category | Device | Specs/Notes |
| :--- | :--- | :--- |
| **Server** | **HP Elite Mini Desktop** | Running **Proxmox VE**. Hosts HA OS (VM) & n8n (LXC). |
| **Network** | **Ubiquiti UDM-SE** | Main router & NVR. Manages VLANS (IoT / Main / Guest). |
| **Zigbee** | **Sonoff Zigbee 3.0 Dongle-P** | Coordinator for Zigbee2MQTT. |
| **IoT Clients** | **ESP8266 / ESP32** | Running Tasmota/ESPHome (Custom sensors, switches). |

---

## 🧠 Software & Logic

* **Core:** Home Assistant OS (Virtual Machine).
* **Automation Engine:** Mixed approach using native HA Automations for immediate triggers and **n8n** for complex logic/data processing.
* **Database:** MariaDB (Recorder).
* **Visuals:** Lovelace with minimal, mobile-first design.

---

## 🌟 Featured Projects

Here are some of the custom solutions and hardware hacks documented in this repo:

### 1. 🌬️ [The Smart DIY Atomizer](./docs/HARDWARE_MOD.md)
A hardware hack converting a proprietary "Miyoscent" aroma diffuser into a smart, locally controlled IoT device using a **Sonoff SV**.
* **Tech:** Sonoff SV (5V Mod), Ultrasonic Driver.
* **Logic:** Presence & Motion based activation + 3-month battery life optimization.
* **[View Hardware Guide](./docs/HARDWARE_MOD.md)**

### 2. 📺 [TV Silence Protocol](./automations/tv_silence/README.md)
An enforcement policy for the LG OLED TV to prevent loud volume accidents in the early morning.
* **Tech:** LG WebOS Integration.
* **Logic:** Dual-automation architecture (Manager + Enforcer) to prevent loops and ensure silence between 05:00-07:00.

---

## 📂 Repository Structure

```text
/config
├── automations.yaml      # Simple automations
├── scenes.yaml           # Lighting scenes
├── scripts.yaml          # Complex sequences
├── /automations          # Split configuration for complex logic
│   ├── /tv_silence       # TV Volume Logic
│   └── /atomizer         # Scent Diffuser Logic
├── /docs                 # Hardware documentation & diagrams
└── /www                  # Custom assets
```
## 🚀 Future Plans / To-Do
[ ] Gardening: Smart irrigation logic for outdoor plants.

[ ] Energy: Detailed power monitoring dashboard via Grafana.

[ ] Voice: Local voice control implementation (Willow/Assist).
