# Home Assistant Blueprints

This repository contains Home Assistant blueprints for automations.

## Blueprints

| Integration | Description                       | Installation |
| ---         | ---                               | ---
| N-Way Interrupts | Sets multiple switches to control a light entity | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/n_way_irq.yaml) |
| Simple Climate Automation | Day/night climate with split room/outside sensors, independent day/night boundaries, manual lock, window ventilation, and forecast-temperature night mode | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/climate-automation.yaml) |
| Shutter Emulation | Automation for no-feedback RF Shutter/Window/Curtain controllers (using TasmotaRF) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/shutter_helper_automation.yaml) |
| Light Timer | Turns off after configurable time (day and night times) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/timed-light.yaml) |
| Alarm Panel Actions | Run user-defined actions per alarm_control_panel state (disarmed, arming, pending, armed_*, triggered) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/alarm-panel-actions.yaml) |
| Daily Openings Alert | Daily reminder/alert if a sensor indicates an opening (door/window/etc.) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/daily-openings-alert.yaml) |
| Soil Moisture Irrigation | Once-daily irrigation with soil waterfall, wind wait, and forecast gates | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/soil-moisture-irrigation.yaml) |
| Motion Activated Lights | Single sensor; day/night auto-on, auto-off, optional day savings | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/motion-activated-lights.yaml) |
| Local Weather MQTT Publisher | Aggregate sensors and forecasts; publish MQTT weather discovery and calculated rain metrics | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/mqtt-local-weather.yaml) |
| Attribute to Sensor | Publish entity attributes over MQTT; discovery creates sensors named `entity_id_attribute` | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/attribute_to_sensor.yaml) |


## Usage

Click the button next to each blueprint to import it directly into your Home Assistant instance.

See each YAML file for details and configuration options.
