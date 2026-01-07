# 🎨 Home Assistant Frontend & Dashboards

[![HACS](https://img.shields.io/badge/HACS-Custom-orange.svg?style=flat&logo=home-assistant)](https://hacs.xyz/)
[![Lovelace](https://img.shields.io/badge/Lovelace-UI-red.svg?style=flat&logo=home-assistant)](https://www.home-assistant.io/lovelace/)

This folder contains custom Lovelace cards, themes, and dashboard configurations used in my Home Assistant setup.

---

## 🧩 Components List

| Component | Type | Description | Dependencies |
| :--- | :--- | :--- | :--- |
| **[News Ticker](https://github.com/r0eenac/Home-Assistant/blob/main/Frontend/News_Ticker.yaml)** | Custom Card | A scrolling news bar (marquee style) like a TV news channel. | `button-card`, `card-mod` |

---

## 🗞️ Scrolling News Ticker Card

A dynamic, scrolling news ticker designed to sit at the bottom or top of your dashboard. It pulls data from a binary sensor attributes and animates the text using CSS.
### 📷 Preview
![Animation](https://github.com/user-attachments/assets/c334cc0d-918f-496a-b223-a7772cbaf840)

### ⚡ Prerequisites (Required via HACS)
To use this card, you must have the following frontend integrations installed:
1.  **[Button Card](https://github.com/custom-cards/button-card)** (`custom:button-card`)
2.  **[Card Mod](https://github.com/thomasloven/lovelace-card-mod)** (`card-mod`)

### 📋 Configuration
This card relies on a specific sensor entity. Ensure you have the following entity available (or update the YAML code to match yours):

* **Entity:** `binary_sensor.news_sensor`
* **Attribute:** `msg1` (The text content to scroll)

### 💻 Usage
View or copy the full YAML code from the link below:

👉 **[News_Ticker.yaml](https://github.com/r0eenac/Home-Assistant/blob/main/Frontend/News_Ticker.yaml)**

Copy the content into a **Manual Card** in your dashboard.

> **Note on RTL/LTR:**
> The card is forced to `direction: ltr` in the CSS to ensure the scrolling animation (`translateX`) works smoothly from right to left without glitching, even if your Home Assistant is in Hebrew.

---

