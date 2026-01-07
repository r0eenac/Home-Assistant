# 🛡️ Failed Login Alert via Telegram

[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Automation-blue?style=flat&logo=home-assistant)](https://www.home-assistant.io/)
[![Security](https://img.shields.io/badge/Category-Security-red?style=flat&logo=security)](https://github.com/topics/security)

This automation is designed to enhance your home server security. It monitors failed login attempts to the Home Assistant interface in real-time and sends an immediate Telegram notification with the attempt details (IP address, etc.).

---

## ⚡ How it works
The automation listens for system notifications (`persistent_notification`) generated within Home Assistant.
1. **Trigger:** When a new notification is created in the system.
2. **Condition:** The system checks if the notification title contains the words "Login attempt".
3. **Action:** A message is sent to the Telegram bot with the original error content.

## 📋 Prerequisites
* **Telegram Bot** integration configured in Home Assistant.
* Your Telegram `chat_id` (you must replace the placeholder number in the code).

## 🛠️ Automation Code (YAML)
```yaml
alias: "Failed Login attempt"
description: "Sends a Telegram notification upon a failed login attempt"
mode: single

trigger:
  - platform: persistent_notification
    update_type:
      - added
    notification_id: ""

condition:
  - condition: template
    value_template: |
      {{ 'Login attempt' in trigger.notification.title }}

action:
  - service: telegram_bot.send_message
    data:
      title: "🚨 Failed Login !!!"
      target: -12345678  # Replace with your Chat ID
      message: "{{ trigger.notification.message }}"

```

or if using secrets 
```yaml
action:
  - data:
      title: Failed Login !!!
      target: !secret telegram_chat_id  
      message: "{{ trigger.notification.message }}"
    action: telegram_bot.send_message
```
םר
## Note: It is recommended to ensure that the Home Assistant IP ban feature (ip_ban_enabled) is enabled in your configuration.yaml for added protection.
