# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added
- Initial ESPHome configuration for the M5Stack Atom Echo.
- `Alarm Siren` template switch that loops an RTTTL alarm tone through the onboard I2S speaker and flashes the
  SK6812 LED red while active.
- `Siren Duration` number entity (1-300s, default 30s) controlling how long the alarm plays before auto-stopping.
- Physical button cancels the alarm instantly on press.
- Local `web_server` UI for control without Home Assistant.
- Home Assistant API integration with encrypted connection and OTA updates.
