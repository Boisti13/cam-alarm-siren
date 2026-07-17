# cam-alarm-siren

ESPHome firmware that turns an [M5Stack Atom Echo](https://github.com/m5stack/ATOM-ECHO) into a WiFi-connected alarm
siren. Built to sound off when a Home Assistant camera motion sensor trips, but the `Alarm Siren` switch works from
any Home Assistant automation or the dashboard directly.

## Hardware

M5Stack Atom Echo (ESP32-PICO-D4), using its onboard:

- I2S speaker (NS4168) — plays the alarm tone
- SK6812 RGB LED — flashes red while the alarm is active
- Physical button — press to silence the alarm instantly

| Peripheral   | Pin              |
|--------------|------------------|
| I2S BCLK     | GPIO19           |
| I2S LRCLK    | GPIO33           |
| I2S DOUT     | GPIO22           |
| RGB LED      | GPIO27           |
| Button       | GPIO39           |

## Features

- **Alarm Siren switch** — loops a two-tone RTTTL siren through the speaker and flashes the LED red while on.
- **Siren Duration number** (1–300s, default 30s) — the alarm auto-stops after this long. Only adjustable from
  Home Assistant (no local web UI).
- **Alarm Cooldown number** (0–300s, default 15s) — after the alarm stops (by duration, button, or motion
  clearing), motion won't re-trigger it again until this cooldown elapses. Doesn't affect manually turning the
  siren on/off yourself.
- **Alarm Volume number** (0–100%, default 80%) — adjusts the siren's playback volume. Takes effect on the next
  loop of the siren tone (~1s), only adjustable from Home Assistant.
- **Button cancel** — a press of the physical button silences the alarm immediately.
- Native Home Assistant API integration (auto-discovered via mDNS) and OTA updates.

## Setup

1. Install the [ESPHome CLI](https://esphome.io/guides/installing_esphome):
   ```
   pip install esphome
   ```
2. Copy `secrets.yaml.example` to `secrets.yaml` and fill in your own values:
   - `wifi_ssid` / `wifi_password` — your WiFi credentials
   - `ap_password` — password for the fallback hotspot if WiFi fails
   - `cam_alarm_siren_key` — generate with `openssl rand -base64 32`

   `secrets.yaml` is gitignored — never commit it.
3. First flash requires a USB connection:
   ```
   esphome run cam-alarm-siren.yaml
   ```
   Subsequent updates can go over-the-air:
   ```
   esphome upload cam-alarm-siren.yaml --device cam-alarm-siren.local
   ```

## Wiring it to a motion sensor

No Home Assistant automation is needed — the device handles this itself. `cam-alarm-siren.yaml` includes
[ha_entities.yaml](ha_entities.yaml) as its `substitutions:`, which defines `cam_motion_entity`: the Home Assistant
motion `binary_sensor` to watch. A `homeassistant` binary_sensor platform mirrors that entity's state onto the
device directly, and its `on_press`/`on_release` actions turn the `Alarm Siren` switch on/off in response —
entirely within the ESPHome config.

To point this at your own motion sensor, just change `cam_motion_entity` in `ha_entities.yaml` to its entity_id
and reflash. Requires Home Assistant's API connection to be up (native ESPHome integration, auto-discovered via
mDNS) since that's how the device reads the entity's state.

## License

MIT — see [LICENSE](LICENSE).
