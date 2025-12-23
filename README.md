# ⏱️ PlayTime Timer for Home Assistant

A reboot-proof countdown timer that controls a smart switch. Perfect for managing screen time, appliance run times, or any timed activity! 🎮⚡

## ✨ Features

- 🛡️ **Reboot-proof**: Uses target end time rather than counting seconds, so the timer survives Home Assistant restarts
- ⏸️ **Pause/Resume**: Turn the switch off to pause, on to resume - time is preserved
- 📚 **Stackable time**: Press "+5 Minutes" multiple times to accumulate time before or during operation
- 📊 **Display-ready**: Hours and Minutes sensors for easy integration with dashboards or ESPHome displays

## 🔧 How It Works

| Action | Result |
|--------|--------|
| Press "+5 Minutes" 🔘 (switch off) | ➕ Adds 5 minutes to the set time |
| Press "+5 Minutes" 🔘 (switch on) | ⏩ Extends the running timer by 5 minutes |
| Turn switch ON 🟢 | ▶️ Starts/resumes the countdown |
| Turn switch OFF 🔴 | ⏸️ Pauses and saves remaining time |
| Press "Reset" 🔄 | 🧹 Clears timer to 0:00 |
| Timer reaches 0:00 ⏰ | 🔌 Switch turns off automatically, timer resets |

## 📁 Files

| File | Purpose |
|------|---------|
| 📄 `playtime_buttons.yaml` | Input buttons for +5 Minutes and Reset |
| 📄 `playtime_number.yaml` | Stores the set/paused time in seconds |
| 📄 `playtime_timekeeper.yaml` | Stores the target end datetime |
| 📄 `playtime_sensors.yaml` | Template sensors for remaining time, hours, minutes, display |
| 📄 `playtime_automations.yaml` | All the logic for buttons, start, pause, and expiry |

## 📥 Installation

### 1️⃣ Step 1: Copy Files

📋 Copy all five `playtime_*.yaml` files to your Home Assistant config directory (or a subdirectory of your choice).

### 2️⃣ Step 2: Configure Your Switch

🔌 Edit `playtime_automations.yaml` and `playtime_sensors.yaml` to replace the switch entity with your own smart plug.

**🔍 Find and replace all instances of:**
```
switch.pc_fing_one_switch
```

**✏️ With your switch entity, for example:**
```
switch.game_console_plug
```

There are **6 occurrences** to replace:
- 📄 `playtime_automations.yaml`: 5 occurrences
- 📄 `playtime_sensors.yaml`: 1 occurrence

### 3️⃣ Step 3: Add Includes to configuration.yaml

⚙️ Add these lines to your `configuration.yaml`:

```yaml
input_button: !include playtime_buttons.yaml
input_number: !include playtime_number.yaml
input_datetime: !include playtime_timekeeper.yaml
template: !include playtime_sensors.yaml
automation: !include playtime_automations.yaml
```

**💡 If you already have these sections**, use one of these methods:

**Option A - Include directory (recommended for multiple files):** 📂
```yaml
input_button: !include_dir_merge_named input_buttons/
input_number: !include_dir_merge_named input_numbers/
input_datetime: !include_dir_merge_named input_datetimes/
template: !include_dir_merge_list templates/
automation: !include_dir_merge_list automations/
```

**Option B - Manual merge:** ✍️

Add the contents of each file directly into your existing configuration sections.

### 4️⃣ Step 4: Validate and Restart

1. 🛠️ Go to **Developer Tools → YAML**
2. ✅ Click **Check Configuration**
3. 🔄 If valid, click **Restart**

## 🏷️ Entities Created

After installation, you'll have these entities:

| Entity | Type | Purpose |
|--------|------|---------|
| `input_button.playtime_add_5` | Button | Add 5 minutes |
| `input_button.playtime_reset` | Button | Reset to 0:00 |
| `input_number.playtime_set_seconds` | Number | Stored time (internal) |
| `input_datetime.playtime_end_time` | Datetime | Target end time (internal) |
| `sensor.playtime_remaining_seconds` | Sensor | Remaining seconds |
| `sensor.playtime_hours` | Sensor | Hours component |
| `sensor.playtime_minutes` | Sensor | Minutes component |
| `sensor.playtime_display` | Sensor | Formatted as "H:MM" |

## 📊 Dashboard Example

```yaml
type: entities
title: PlayTime Timer
entities:
  - entity: sensor.playtime_display
    name: Time Remaining
  - entity: input_button.playtime_add_5
    name: Add 5 Minutes
  - entity: input_button.playtime_reset
    name: Reset
  - entity: switch.your_switch_here
    name: Power
```

## 🖥️ ESPHome Display Integration

💻 To show the timer on an ESPHome display:

```yaml
sensor:
  - platform: homeassistant
    id: playtime_hours
    entity_id: sensor.playtime_hours

  - platform: homeassistant
    id: playtime_minutes
    entity_id: sensor.playtime_minutes

# In your display lambda:
display:
  - platform: ...
    lambda: |-
      it.printf(0, 0, id(my_font), "%d:%02d", 
        (int)id(playtime_hours).state, 
        (int)id(playtime_minutes).state);
```

## 🔢 Multiple Timers

To create additional timers for different switches:

1. 📑 Duplicate all five files with a new prefix (e.g., `gametime_*.yaml`)
2. 🔄 Find and replace `playtime` with your new name (e.g., `gametime`)
3. 🔌 Update the switch entity to your target device
4. ➕ Add the new includes to `configuration.yaml`

## 🔧 Troubleshooting

**⚠️ Timer not counting down:**
- Ensure `playtime_sensors.yaml` is included under `template:` (not `sensor:`)
- Check that the trigger-based template is formatted correctly

**⚠️ Timer resets on pause:**
- ✅ Verify you're using `playtime_set_seconds` (not minutes)
- ✅ Check the "Pause on Switch Off" automation is enabled

**⚠️ Switch doesn't turn off at 0:00:**
- ✅ Verify the switch entity name is correct in `playtime_automations.yaml`
- ✅ Check that the "PlayTime - Expired" automation is enabled

## 📜 License

✨ Free to use and modify. Attribution appreciated but not required.

---

<div align="center">

### 💖 Support This Project

Found this useful? Want to say thanks and fuel future creations?

**Your support keeps the creativity flowing!** 🍺✨

<table>
  <tr>
    <td align="center">
      <a href="https://www.buymeacoffee.com/3ative">
        <img src="https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Support-yellow.svg?style=for-the-badge&logo=buy-me-a-coffee&logoColor=white" alt="Buy Me A Coffee"/>
        <br/>
        <b>☕ Buy me a Coffee</b>
      </a>
    </td>
    <td align="center">
      <a href="https://www.patreon.com/3ative">
        <img src="https://img.shields.io/badge/Patreon-Become%20a%20Patron-red.svg?style=for-the-badge&logo=patreon&logoColor=white" alt="Patreon"/>
        <br/>
        <b>💖 Join on Patreon</b>
      </a>
    </td>
  </tr>
</table>

**Every contribution helps bring more awesome projects to life!** 🚀

</div>

---
