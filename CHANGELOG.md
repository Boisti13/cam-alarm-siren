# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added
- `ha_entities.yaml` — reference doc listing the Home Assistant entities this project depends on (incoming motion
  sensor) and produces (outgoing siren switch), since the motion sensor is a live ONVIF entity that can't be
  defined in code.
- Initial ESPHome configuration for the M5Stack Atom Echo.
- `Alarm Siren` template switch that loops an RTTTL alarm tone through the onboard I2S speaker and flashes the
  SK6812 LED red while active.
- `Siren Duration` number entity (1-300s, default 30s, only adjustable from Home Assistant) controlling how long
  the alarm plays before auto-stopping.
- Physical button cancels the alarm instantly on press.
- Home Assistant API integration with encrypted connection and OTA updates.
- `automation.camera_alarm_siren_pt2_motion` in Home Assistant, wiring `binary_sensor.cam_pt2_motion_alarm`
  motion events to the siren switch.
