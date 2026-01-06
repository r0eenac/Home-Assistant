# 🏠 Roee's Smart Home & Homelab

![Home Assistant](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?style=for-the-badge&logo=home-assistant&logoColor=white)
![n8n](https://img.shields.io/badge/n8n-%23EA4B71.svg?style=for-the-badge&logo=n8n&logoColor=white)
![Proxmox](https://img.shields.io/badge/proxmox-%23E57000.svg?style=for-the-badge&logo=proxmox&logoColor=white)
![Ubiquiti](https://img.shields.io/badge/ubiquiti-%230559C9.svg?style=for-the-badge&logo=ubiquiti&logoColor=white)
![Portainer](https://img.shields.io/badge/portainer-%2329a5fe.svg?style=for-the-badge&logo=portainer&logoColor=white)

> **"Automation should be felt, not seen."**

Welcome to my smart home configuration repository. This setup focuses on **local control**, **data privacy**, and **proactive automation**. The system runs on a robust Proxmox cluster and leverages n8n and AppDaemon for complex logic.

---

## 🖥️ The Hardware Stack

My setup is built for reliability and performance, moving away from Raspberry Pis to enterprise-grade micro-clients.

| Category | Device | Specs/Notes |
| :--- | :--- | :--- |
| **Server** | **HP Elite Mini Desktop** | Running **Proxmox VE**. The heart of the lab. |
| **Power** | **Eaton UPS** | Backup power for Server & Network. Integrated via NUT. |
| **Network** | **Ubiquiti UDM-SE** | Main router & NVR. Manages VLANS (IoT / Main / Guest). |
| **Zigbee** | **Sonoff Zigbee 3.0 Dongle-P** | Coordinator for Zigbee2MQTT. |
| **IoT Clients** | **ESP8266 / ESP32** | Running Tasmota/ESPHome (Custom sensors, switches). |

---

## 🧠 Software & Architecture

The system is layered to ensure separation of concerns and ease of management:

### Infrastructure & Connectivity
* **Virtualization:** Proxmox VE hosting VMs and LXC containers.
* **Container Mgmt:** **Portainer** for visual management of Docker stacks.
* **Remote Access:** **Cloudflare Tunnel** (Zero Trust) for secure external access without open ports.
* **Messaging:** **Mosquitto Broker** handling MQTT traffic between Zigbee devices and HA.

### Logic & Automation
* **Core:** Home Assistant OS.
* **Workflow Engine:** **n8n** (Complex data flows & integrations).
* **Code-Based Logic:** **AppDaemon** (Python-based automations for advanced constraints).

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
├── apps                  # AppDaemon Python apps
├── /automations          # Split configuration for complex logic
│   ├── /tv_silence       # TV Volume Logic
│   └── /atomizer         # Scent Diffuser Logic
├── /docs                 # Hardware documentation & diagrams
└── /www                  # Custom assets
```
