# Home Assistant Blueprints

This repository contains Home Assistant blueprints for automations.

## Blueprints

| Integration | Description                       | Installation |
| ---         | ---                               | ---
| N-Way Interrupts | Sets multiple switches to control a light entity | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/n_way_irq.yaml) |
| Simple Climate Automation | Climate Automation, with setpoint, presence and external reading | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/climate-automation.yaml) |
| Shutter Emulation | Automation for no-feedback RF Shutter/Window/Curtain controllers (using TasmotaRF) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/shutter_helper_automation.yaml) |
| Light Timer | Turns off after configurable time (day and night times) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/timed-light.yaml) |
| Alarm Panel Actions | Run user-defined actions per alarm_control_panel state (disarmed, arming, pending, armed_*, triggered) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/alarm-panel-actions.yaml) |
| Daily Openings Alert | Daily reminder/alert if a sensor indicates an opening (door/window/etc.) | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/daily-openings-alert.yaml) |
| Soil Moisture Irrigation | Once-daily irrigation with soil waterfall, wind wait, and forecast gates | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/soil-moisture-irrigation.yaml) |
| Motion Activated Lights | Multi-sensor motion lights with day/night enable helpers and no-motion timeout | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/motion-activated-lights.yaml) |
| Local Weather MQTT Publisher | Aggregate sensors and forecasts; publish MQTT weather discovery and calculated rain metrics | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/mqtt-local-weather.yaml) |


## Usage

Click the button next to each blueprint to import it directly into your Home Assistant instance.

### Soil Moisture Irrigation

Runs **once per day** at the configured activation time. Each run evaluates conditions, optionally waits for wind to drop, then irrigates or skips. At least one **valve** is required; pumps are optional.

**Override:** optional binary (`input_boolean` or `binary_sensor`). When ON, irrigate immediately and skip all soil, weather, and forecast checks.

**Soil waterfall** (one signal per run, in priority order):

| Priority | Input | Pass condition |
| --- | --- | --- |
| 1 | Soil irrigation request | ON = needs water |
| 2 | Soil moisture entity | Moisture ≤ threshold (%) |
| 3 | Weather entity — rain mm | Accumulated rain < threshold (mm) |
| 4 | Weather entity — rain hours | Rain duration < threshold |
| 5 | Irrigation timeout (enabled) | Days since last valve activity ≥ timeout |

If no configured signal is readable, irrigation is skipped.

**Present weather:** when a weather entity is configured, skip if the current condition is rainy. Missing or invalid data is ignored.

**Wind wait:** when wind speed from the weather entity is at or above the threshold, poll every 5 minutes until it drops or the wait timeout expires. Still too windy afterward → skip.

**Forecast soft fallback** (after soil passes; if all methods unavailable, soil alone decides):

1. **Next 24h rain probability** helper entity — skip when value ≥ threshold
2. **Daily forecast** (today + tomorrow) — skip when either day’s probability ≥ threshold
3. **Forecast condition** (next 24h) — skip on rainy conditions; pass on clear/partly cloudy

**Recommended helpers:** maintain a template sensor for max precipitation probability in the next 24 hours and select it as **Next 24h rain probability**. Use a local weather station or template weather entity when possible; default Home Assistant weather often lacks rain mm, rain hours, or wind data.

**Companion automation example** (populate the 24h probability helper):

```yaml
# Example only — adjust entity IDs and template logic for your setup
trigger:
  - trigger: time_pattern
    hours: "/1"
action:
  - action: weather.get_forecasts
    target:
      entity_id: weather.home
    data:
      type: hourly
    response_variable: wx
  - action: homeassistant.update_entity
    target:
      entity_id: sensor.irrigation_max_rain_prob_24h
```

Define `sensor.irrigation_max_rain_prob_24h` as a template sensor that reads the hourly forecast response (or use a Trigger-based template updated by the automation above).

**Migration from the previous blueprint version:**

| Old input | New input |
| --- | --- |
| Days between irrigation | Enable irrigation timeout + Irrigation timeout |
| Recent rain helper | Removed — use weather entity rain mm attrs (priority 3) |
| Update rain average counter | Removed |
| Max forecast rain (mm) | Removed — forecast uses probability and condition only |
| Max rain probability | Still **Max rain probability** (shared threshold) |

### Local Weather MQTT Publisher

Publishes a consolidated local weather JSON payload over MQTT with discovery messages. Designed to feed irrigation helpers and optional template weather configurations.

**Prerequisites:**
- Home Assistant **MQTT integration** configured (Settings → Devices & services → MQTT). Broker address, port, username, and password are set there — not in this blueprint.
- MQTT discovery enabled in Home Assistant.

**Source resolution:** each measurement uses the first available value from:
1. Dedicated source entity (if configured)
2. Primary forecast weather entity attributes
3. Fallback weather entity attributes
4. `weather.forecast_home` (when enabled)

**Calculated fields** (included in the JSON payload and exposed via MQTT sensor discovery):
- `precipitation_probability_24h` — max hourly forecast probability in the next 24 hours
- `accumulated_rain_mm_7d` — rolling 7-day rain total (requires optional `input_text` rain history helper)
- `accumulated_rain_hours_7d` — rolling 7-day rain duration total

**Rain history helper:** create an `input_text` helper (e.g. `input_text.local_rain_history`) and select it in the blueprint. The automation stores a JSON array of daily `{date, mm, hours}` entries and trims entries older than 7 days.

**MQTT topics** (defaults):
- State: `{topic_prefix}/state` (default `localweather/state`)
- Weather discovery: `homeassistant/weather/{object_id}/config`
- Sensor discovery: `homeassistant/sensor/{object_id}_precip_prob_24h/config`, etc.

**Integration with Soil Moisture Irrigation:** point **Next 24h rain probability** at the discovered precip sensor (e.g. `sensor.local_climate_precip_prob_24h`) or read `precipitation_probability_24h` from the JSON payload.

**Note:** Home Assistant does not natively discover full `weather` entities over MQTT on all versions. The blueprint always publishes working **sensor** discovery entries for the calculated metrics. For a template weather entity, consume the MQTT JSON via a trigger-based template weather configuration outside this blueprint.

---

See each YAML file for details and configuration options.
