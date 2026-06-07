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
| Motion Activated Lights | Multi-sensor motion lights with day/night enable helpers and no-motion timeout | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/motion-activated-lights.yaml) |
| Local Weather MQTT Publisher | Aggregate sensors and forecasts; publish MQTT weather discovery and calculated rain metrics | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/mqtt-local-weather.yaml) |
| Attribute to Sensor | Publish entity attributes over MQTT; discovery creates sensors named `entity_id_attribute` | [![Add to my Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/rftafas/homeassistant-blueprints/main/blueprints/attribute_to_sensor.yaml) |


## Usage

Click the button next to each blueprint to import it directly into your Home Assistant instance.

### Simple Climate Automation

Reactive climate control with **`mode: single`** (triggers during a run are ignored). **Triggers:**

1. **Indoor temperature** (`room_temp_change`) — any state change on **`room_temperature_sensors`**
2. **Outdoor temperature** (`outside_temp_change`) — any state change on **`outside_temperature_sensors`**
3. **Setpoint** (`setpoint_change`) — external day and/or night entity when configured
4. **Day / night boundaries** (`day_begin` / `night_begin`) — **`day_boundary`** (day config) and **`night_boundary`** (night config) are independent: each **sun** or **fixed** clock time
5. **Openings** — optional **NO** / **YES**; **`window_change`** when YES (any sensor state change)
6. **Presence** (`presence_change`) — template trigger when **`occupation_sensor`** is **YES** (any state change)
7. **HVAC configuration** (`hvac_config`) — **`trigger: state`** on **`hvac_mode`**, target **`temperature`**, or **`fan_mode`**; manual lock when occupied
8. **Manual re-evaluation** (`manual`) — optional helper ON forces a run from current state
9. **Day/night enable** (`day_enable_on` / `night_enable_on`) — helper **ON** re-evaluates; **off** does not trigger

**Input sections:** `main_config` (HVAC, optional `manual_trigger`), `temperature_config` (room/outdoor sensors, **`forecast`**), `presence_config` (optional **`occupation_sensor`**, always-visible **`manual_lock`**), `windows_config` (optional `windows_sensors`), `day_config` (optional enable, **`day_boundary`**, `day_set_point`), `night_config` (optional enable, **`night_set_point`**, **`night_boundary`**).

**Presence (`occupation_sensor` choose):** **NO** (default) — no presence routing. **YES** — entity and **`presence_unoccupied_duration`**; **`room_unoccupied`** sets target off after duration; presence **off** skips day/night via climate gate (`is_state on`).

**Windows (`windows_sensors` choose):** **NO** (default). **YES** — sensor list only; **`window_change`** trigger; **`any_window_open`** → vent (`fan_only` + low) in ±2 °C band else off.

**Temperature sensors (separate lists):**

| Input | Role |
| --- | --- |
| **`room_temperature_sensors`** | Indoor — `room_temp` average (day bands) |
| **`outside_temperature_sensors`** | Outdoor — `outside_temp` (ventilation ±2 °C band) |
| **`forecast`** | Night mode — **average** (default, region °C you enter) or **forecast** entity (8 h average) → `forecast_temp` |

**Variables:** `room_temp`, `outside_temp`, `forecast_temp`, `comfort_delta`, `any_window_open`, `room_unoccupied`, `outside_in_ventilation_band`, `daytime_auto_climate_enabled`, `nighttime_auto_climate_enabled`.

**Day vs night:** **`day_boundary`** and **`night_boundary`** are configured separately (each **sun** or **fixed**). **`is_day`** requires both: day side passed (above horizon or `now ≥ day_time`) and night side not ended (above horizon or `now < night_time`). Example: day **sun** + night **fixed** 22:00 → daytime from sunrise until sunset and before 22:00. Night mode uses **`forecast_temp`** from **`forecast`** (**average** or **forecast** entity). Action order: init → manual-lock **`choose`** ( **`hvac_config`** only) → routing **`choose`** (presence → window → **default** climate) → single idempotent apply.

**Day/night enable:** **NO** (default) = always on; **YES** = helper + enable gate on day/night branches. **YES** accepts **`input_boolean`** or **`binary_sensor`** for day and night.

**Night setpoint:** **`night_set_point`** choose — **`day_copy`** (default, uses day setpoint), **`fixed`** (slider), or **`external`** (temperature **`sensor`**, **`number`**, or **`input_number`**).

**Manual lock:** sibling boolean under **`presence_config`** (default on; first option in the manual-lock **`choose`** on **`manual_lock`**, **`trigger.id == 'hvac_config'`**, and **`not room_unoccupied`**). When on and occupied, a user **HVAC mode / temp / fan** change pauses automatic climate: **with presence YES** — wait up to **4 hours** or until unoccupied; **without presence** — full **4 hours** (no early exit). When manual lock matches, wait runs first; routing **`choose`** always runs next (wait-then-route). Echo from this automation’s own `climate.set_*` is suppressed by **`mode: single`** plus the **5 s delay** after commanded services.

**HVAC availability:** Top-level [automation `conditions:`](https://www.home-assistant.io/docs/automation/condition/) skip the run if **any** configured climate entity is `unavailable` or `unknown` (all must be reachable before actions execute).

**Manual trigger (optional):** `manual_trigger` choose — **NO** (default) or **YES** with an `input_boolean` / `binary_sensor`. When the helper turns **ON**, the automation re-evaluates using **current state** (priority order):

| Condition | Behavior |
| --- | --- |
| Presence **YES** and `off` | Climate skipped; targets stay default off unless window/long-unoccupied overrides |
| Presence **NO**, or occupied, no window open, lock off | Normal day/night bands set **`target_*`** |

After any commanded `climate.set_*`, a **5 s delay** runs before the run ends.

**Ventilation:** fixed ±2 °C from base `setpoint` (not night offset); no blueprint input.

Does **not** set manual lock (only `hvac_config` when `manual_lock` is on). External example:

```yaml
action:
  - action: input_boolean.turn_on
    target:
      entity_id: input_boolean.climate_recheck
```

**Windows/doors (optional):** **YES** — **`any_window_open`** (second routing **`choose`** option) → vent band or **off** via **`target_*`**; climate default skipped while open (or when unoccupied — presence option wins first).

**HVAC apply:** Single idempotent block at end — `climate.set_temperature` (+ `climate.set_fan_mode` only when **`target_mode != 'off'`**). **5 s delay** only after a service runs.

**Breaking changes (re-save required):**

| Old | New |
| --- | --- |
| `room_temperature` choose | `room_temperature_sensors` (indoor multi-entity) |
| `outside_temperature` choose | `outside_temperature_sensors` (outdoor multi-entity) |
| `hvac_config` any state change | **`trigger: state`** on `hvac_mode` / `temperature` / `fan_mode` only |
| `parent_id` manual-lock guard | Removed — `mode: single` + 5 s post-command delay |
| `temp_source` (single choose) | (superseded by separate sensor lists above) |
| `measured_temp` | `room_temp` (+ `outside_temp` for ventilation) |
| `window_suspend_duration` / `windows_sensors.YES.window_open_duration` | Removed — action uses **`any_window_open`** only |
| `window_suspend` trigger | **`window_change`** + **`any_window_open`** |
| `automation_suspended` | `any_window_open` (climate gate while open) |
| `measured_temp_change` | `room_temp_change` + `outside_temp_change` |
| `ventilation_band` input | Removed — fixed ±2 °C from setpoint |
| `temp_set_point` in `threshold_config` | `day_set_point` in `day_config` |
| `night_offset` choose / `night_setpoint_independent` | **`night_set_point`**: **`day_copy`** / **`fixed`** / **`external`** |
| `occupation_sensor` entity | Optional **NO** / **YES** object (`entity`, duration) |
| `presence_unoccupied_duration` (top-level) | `occupation_sensor.YES.presence_unoccupied_duration` |
| `occupation_sensor.YES.manual_lock` | **`manual_lock`** sibling under `presence_config` |
| `windows_sensors` (required list) | Optional **NO** / **YES** object (`sensors` only) |
| `windows_sensors.YES.window_open_duration` | Removed (was unused) |
| — | `manual_trigger` optional helper (new) |
| `daytime_auto_climate_enable` / `nighttime_auto_climate_enable` `boolean:` | Optional **NO** / **YES** + helper; **YES**: **`input_boolean`** or **`binary_sensor`** |
| `day_start` / `night_start` / `period_boundary` | **`day_boundary`** + **`night_boundary`** (independent sun/fixed per period) |
| `forecast_source` choose | **`forecast`** in **`temperature_config`** |
| `weather_entity` | Removed — use **forecast** branch + template sensor, or **average** (default) |
| `forecast_temperature_sensors` | **`forecast`** — **average** / **forecast** choose |
| `forecast_temperature` | **`forecast`** (renamed) |
| `forecast_temperature` **outdoor_copy** / **external** | **average** / **forecast** |
| `presence_off` / `presence_on` | **`presence_change`** + **`room_unoccupied`** variable |
| `window_open` / `window_close` | **`window_change`** + **`any_window_open`** |
| `mode: restart` | `mode: single` |

### Soil Moisture Irrigation

Runs **once per day** at the configured activation time. Each run evaluates conditions, optionally waits for wind to drop, then irrigates or skips. At least one **valve** entity is required (`valve.open_valve` / `valve.close_valve`). **Pumps** are optional (**NO** / **YES** choose); when YES, pump switches turn on and off with each run.

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

**Wind wait:** when wind speed from the weather entity is at or above the threshold (km/h, matching HA weather `wind_speed`), poll every 5 minutes until it drops or the wait timeout expires. Still too windy afterward → skip. Default threshold is **29 km/h** (~8 m/s).

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

### Attribute to Sensor

Publishes selected entity **attributes** to MQTT and registers **sensor** entities via MQTT discovery. Use when an integration exposes useful data only as attributes (weather, climate, device diagnostics) and you want a dedicated `sensor.*` entity.

**Prerequisites:**
- Home Assistant **MQTT integration** configured (Settings → Devices & services → MQTT).
- MQTT discovery enabled (default).
- Broker **address**, **port**, **username**, and **password** in the blueprint must match the MQTT integration (Reconfigure if needed).

**Configuration:**
- **Entity attribute mappings** — add one or more rows: pick an entity, then enter the exact attribute key (from Developer Tools → States). Blueprints cannot chain the attribute picker to a per-row entity; use the text field.
- **MQTT** — **MQTT broker address**, **MQTT port** (default `1883`), **MQTT username**, and **MQTT password**; optional `discovery_prefix` (default `homeassistant`), `topic_prefix` (default `attribute_to_sensor`), and **Retain state messages**.

**Naming:** discovery `name` is `entity_id_attribute` (e.g. `sensor.outdoor_temp_native_value`). MQTT `object_id` replaces dots with underscores: `sensor_outdoor_temp_native_value`.

**Triggers:**
1. **Home Assistant start** — publish discovery (retained) and current values for every mapping.
2. **`state_changed` events** — when a mapped attribute value changes on a mapped entity, publish the new value only for matching rows.

**MQTT topics** (defaults, per mapping):

| Purpose | Topic |
| --- | --- |
| Discovery | `homeassistant/sensor/{object_id}/config` |
| State | `attribute_to_sensor/{object_id}/state` |

Example: entity `sensor.outdoor_temp`, attribute `native_value` → state topic `attribute_to_sensor/sensor_outdoor_temp_native_value/state`, `unique_id` `attribute_to_sensor_sensor_outdoor_temp_native_value`.

**Removing a sensor:** delete the mapping row and re-save does **not** remove the MQTT entity. Publish an **empty retained** message to the old discovery config topic (e.g. `homeassistant/sensor/sensor_outdoor_temp_native_value/config`).

**Mode:** `parallel` — concurrent attribute updates publish independently.

---

See each YAML file for details and configuration options.
