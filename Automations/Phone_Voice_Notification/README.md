
# HA-Critical-TTS: Bypass Silent Mode for Urgent Alerts

A robust Home Assistant automation logic to ensure your mobile device speaks out loud during emergencies, even if it's set to silent or "Do Not Disturb" mode.
The automation uses Jinja2 templates to fetch dynamic content from sensor attributes, making it perfect for RSS feeds, news flashes, or personalized weather reports.
<img width="756" height="694" alt="image" src="https://github.com/user-attachments/assets/5e536680-9bbf-4d63-9b78-96c7672d13c2" />

## 📖 Overview
Standard notifications are easily missed. This project provides a blueprint for sending Text-to-Speech (TTS) messages to the Home Assistant companion app by targeting the **Alarm Audio Stream**, which bypasses most system-level volume restrictions.

### Key Features:
* **Silence Bypass:** Uses the `alarm_stream` to ensure the message is heard.
* **Volume Control:** Automatically sets the alarm volume to a specific level before speaking.
* **Priority Handling:** High-priority delivery to wake the device immediately.

---

## 🛠 Prerequisites
* [Home Assistant](https://www.home-assistant.io/) instance.
* [Home Assistant Companion App](https://companion.home-assistant.io/) installed on an Android device.
* Mobile App integration properly configured.

---

## 🚀 Implementation

### 1. The Automation Logic (YAML)
Copy this into your `automations.yaml` or use the UI editor. Replace `your_phone_id` with your actual device notify service.

```yaml
alias: "Send Critical TTS Notification"
description: "Sets volume, speaks, and restores volume level"
trigger: [] # כאן תוסיף את הטריגר שלך (למשל חיישן הצפה)

action:
  # שלב 1: העלאת ווליום למקסימום
  - service: notify.mobile_app_sm_xyz
    data:
      message: "command_volume_level"
      data:
        media_stream: "alarm_stream"
        command: 100

  - service: notify.mobile_app_sm_xyz
    data:
      title: "CRITICAL ALERT"
      message: "TTS"
      data:
        ttl: 0
        priority: high
        media_stream: "alarm_stream"
        tts_text: "Attention! A critical event has been detected at home."

  - delay: "00:00:05"

  - service: notify.mobile_app_sm_xyz
    data:
      message: "command_volume_level"
      data:
        media_stream: "alarm_stream"
        command: 10

mode: single
```


## ⚙️ Device Settings (Crucial for Hebrew Support)
To get the best voice quality and ensure Hebrew is supported correctly, you must adjust the Text-to-Speech settings on your Android device:

1. **Switch to Google TTS:**
   - Go to **Settings** > **General Management** > **Text-to-speech**.
   - Change the **Preferred engine** from *Samsung TTS* to **Speech Services by Google**.
   
2. **Download Hebrew Voice Data:**
   - Tap the **Settings icon** next to "Speech Services by Google".
   - Select **Install voice data**.
   - Locate **Hebrew** and download the voice pack.

3. **Verify Language:**
   - Ensure the default language is set to "Use system language" or "Hebrew".
  
### ⚠️ Common Issue: Hebrew TTS not working/sounding robotic?
If you've switched to Google TTS and downloaded the Hebrew voice, but it still doesn't work or sounds like a broken robot, follow this "Power Reset" for the TTS engine:

1. **Delete the Voice Pack:** Go to *Settings > Text-to-speech > Google Engine Settings > Install voice data*, find **Hebrew**, and delete it.
2. **Uninstall Google Speech Services:** Go to the Google Play Store, search for **"Speech Services by Google"**, and click **Uninstall** (this will remove the updates and revert to the factory version).
3. **Re-install/Update:** Click **Update** in the Play Store to get the latest version.
4. **Re-download Hebrew:** Go back to the TTS settings and download the Hebrew voice pack again.
5. **The "Wake up" call:** This process forces the system to re-index the voice files and usually "wakes up" the system to recognize Hebrew properly.

## 💡 Use Cases
This automation is ideal for:

Water Leak Detection: Immediate voice alert to prevent damage.

Security Breaches: Notifying you when the house is in "Away" mode and motion is detected.

Fire/Smoke Alarms: Life-saving announcements during the night.

## 🔧 Troubleshooting
Android Only: Note that the alarm_stream control is highly effective on Android. iOS has different restrictions for critical alerts.

Permission: Ensure the Home Assistant app has permission to "Draw over other apps" and modify "Do Not Disturb" settings if necessar
