# cam-alarm-siren

ESPHome firmware that turns an [M5Stack Atom Echo](https://github.com/m5stack/ATOM-ECHO) into a WiFi-connected alarm
siren. Built to sound off when a Home Assistant camera motion sensor trips, but the `Alarm Siren` switch works from
any automation, the device's own web UI, or Home Assistant directly.

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
- **Siren Duration number** (1–300s, default 30s) — the alarm auto-stops after this long. Adjustable from Home
  Assistant, or directly from the device's own web UI.
- **Button cancel** — a press of the physical button silences the alarm immediately.
- **Local web UI** (`web_server`) — reachable at `http://cam-alarm-siren.local` for control/monitoring without
  Home Assistant.
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

## Wiring it to a motion sensor in Home Assistant

Once flashed, the device is auto-discovered by Home Assistant's ESPHome integration. Create an automation that
triggers the `switch.camera_alarm_siren_alarm_siren` entity when your motion sensor activates, e.g. using the
purpose-specific `motion.detected` / `motion.cleared` triggers. See [ha_entities.yaml](ha_entities.yaml) for the
entities this project expects on the Home Assistant side:

```yaml
triggers:
  - trigger: motion.detected
    target:
      entity_id: binary_sensor.your_motion_sensor
    id: motion_on
  - trigger: motion.cleared
    target:
      entity_id: binary_sensor.your_motion_sensor
    id: motion_off
actions:
  - choose:
      - conditions:
          - condition: trigger
            id: motion_on
        sequence:
          - action: switch.turn_on
            target:
              entity_id: switch.camera_alarm_siren_alarm_siren
      - conditions:
          - condition: trigger
            id: motion_off
        sequence:
          - action: switch.turn_off
            target:
              entity_id: switch.camera_alarm_siren_alarm_siren
```

## License

MIT — see [LICENSE](LICENSE).
