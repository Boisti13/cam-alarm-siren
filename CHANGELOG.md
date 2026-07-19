# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added
- `ha_entities.yaml` — ESPHome `substitutions:` file defining `cam_motion_entity`, the Home Assistant motion
  binary_sensor this device watches, centralized in one place instead of hardcoded inline.
- Initial ESPHome configuration for the M5Stack Atom Echo.
- `Alarm Siren` template switch that loops an RTTTL alarm tone through the onboard I2S speaker and flashes the
  SK6812 LED red while active.
- `Siren Duration` number entity (1-300s, default 30s, only adjustable from Home Assistant) controlling how long
  the alarm plays before auto-stopping.
- `Alarm Cooldown` number entity (0-300s, default 15s) gating how soon motion can re-trigger the siren after it
  stops. Manual on/off (button, dashboard) is unaffected.
- `Alarm Volume` number entity (0-100%, default 80%) controlling siren playback volume via the rtttl component's
  `set_gain()`, only adjustable from Home Assistant.
- Standby LED state (default green, 15% brightness) shown whenever the alarm isn't active, applied at boot and
  again once the device connects to Home Assistant. Reuses the existing `Status LED` light entity, so its
  color/brightness are adjustable from Home Assistant directly; the last-set values persist across alarms and
  reboots via globals with `restore_value`. The LED switches to full-brightness red blinking during an active
  alarm and returns to standby once it stops.
  - Capturing the standby color from the light's `on_state` is gated on `booted && !alarm_active`, not just
    `!alarm_active`. Without the `booted` half of that gate, the light's own state-publish during its `setup()`
    (which defaults to full white/on, before anything has explicitly set a color) gets captured as "standby"
    before `on_boot` ever runs — permanently overwriting the real standby color with white on every subsequent
    boot, since it's persisted via `restore_value`. Also gated the write side the same way, since the switch's
    own `restore_mode: ALWAYS_OFF` invokes `turn_off_action` once during its own `setup()`, before boot finishes.
  - Sets color/brightness via a single lambda building the `LightCall` directly (`set_rgb()`/`set_brightness()`)
    rather than four separate `!lambda`-templated `red:`/`green:`/`blue:`/`brightness:` fields on one
    `light.turn_on:` action, which proved unreliable for this platform.
  - `safe_mode: boot_is_good_after: 5s` (default 60s) shrinks the window in which an unrelated reset before boot
    is confirmed would cause ESP-IDF to roll back to a previous OTA image.
- Physical button cancels the alarm instantly on press.
- A `homeassistant` binary_sensor mirrors the motion sensor's state onto the device directly, driving the siren
  switch on `on_press`/`on_release` — no Home Assistant automation needed.
- Home Assistant API integration with encrypted connection and OTA updates.

### Removed
- The Home Assistant-side automation wiring motion events to the siren switch, superseded by the device handling
  it directly via the `homeassistant` binary_sensor platform.
