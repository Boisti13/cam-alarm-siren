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
- Physical button cancels the alarm instantly on press.
- A `homeassistant` binary_sensor mirrors the motion sensor's state onto the device directly, driving the siren
  switch on `on_press`/`on_release` — no Home Assistant automation needed.
- Home Assistant API integration with encrypted connection and OTA updates.

### Removed
- The Home Assistant-side automation wiring motion events to the siren switch, superseded by the device handling
  it directly via the `homeassistant` binary_sensor platform.
