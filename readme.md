# NWS Weather Alerts - Voice Announcements & WLED Warning Light

A Home Assistant automation blueprint for National Weather Service alerts.

This blueprint announces new NWS alerts on Home Assistant Voice / Assist satellite devices and can flash a WLED-controlled RGB/RGBW light strip when room presence is detected. It also supports an optional helper toggle so you can quickly enable or disable weather alert notifications from your dashboard.

After flashing the WLED strip, the blueprint restores the light to its previous state.

## Features

* Announces new NWS alerts on one or more Home Assistant Voice / Assist satellite devices
* Flashes a WLED light strip only when a selected occupancy or presence sensor is active
* Uses different warning colors for different alert types
* Restores the previous WLED light state after the alert flash sequence
* Optional master enable/disable helper toggle
* Only triggers when the active alert count increases
* Configurable blink count, blink speed, and brightness
* Works well for office, desk, gaming room, workshop, or bedroom alert lighting

## Example Use Case

You have Home Assistant Voice devices around the house and a WLED LED strip above your desk.

When a new NWS weather alert is issued:

1. Your selected voice assistants announce the alert.
2. If your office presence sensor detects you are in the room, the LED strip flashes in an alert-specific color.
3. After the flash sequence finishes, the LED strip returns to its previous state.

## Requirements

This blueprint expects the following Home Assistant entities or integrations:

* NWS Alerts integration sensor
* Home Assistant Voice / Assist satellite entity
* WLED light entity
* Occupancy or presence binary sensor
* Optional input_boolean helper toggle

## Recommended Optional Helper

Create a toggle helper if you want dashboard control over whether weather alerts run.

In Home Assistant:

1. Go to **Settings**
2. Go to **Devices & services**
3. Go to **Helpers**
4. Select **Create helper**
5. Choose **Toggle**
6. Name it something like:

```text
Weather Alerts
```

This will usually create an entity like:

```yaml
input_boolean.weather_alerts
```

When creating an automation from this blueprint, set:

```text
Use Enable Helper: enabled
Enable Helper Entity: input_boolean.weather_alerts
```

If the helper is on, alerts can run.
If the helper is off, the automation will be blocked.

## Alert Colors

Default alert color mapping:

| Alert Type                  | Color            |
| --------------------------- | ---------------- |
| Tornado Warning             | Magenta / Purple |
| Tornado Watch               | Orange           |
| Severe Thunderstorm Warning | Red              |
| Severe Thunderstorm Watch   | Yellow           |
| Flash Flood / Flood Warning | Blue             |
| Winter Storm / Snow / Ice   | Cyan             |
| Other Alerts                | White            |

## Blueprint Inputs

### NWS Alert Sensor

The sensor from the NWS Alerts integration. The sensor state should represent the number of active alerts.

### Use Enable Helper

Optional boolean setting. Turn this on if you want to use a helper toggle to control whether alerts run.

### Enable Helper Entity

Optional `input_boolean` helper. This is only used when **Use Enable Helper** is enabled.

Example:

```yaml
input_boolean.weather_alerts
```

### Office Occupancy Sensor

A binary sensor used to decide whether the WLED strip should flash.

Example entities:

```yaml
binary_sensor.office_occupancy
binary_sensor.office_presence
binary_sensor.desk_motion
```

The WLED strip only flashes when this sensor is `on`.

### WLED Light

The WLED light entity to flash during the alert.

Example:

```yaml
light.desk_wled
```

### Voice Assist Satellites

One or more Home Assistant Voice / Assist satellite entities that should announce the alert.

Example:

```yaml
assist_satellite.office_voice
assist_satellite.living_room_voice
```

### Alert List Attribute

The attribute on the NWS sensor containing the list of active alerts.

For many NWS Alerts integrations, this is:

```text
Alerts
```

If your alert text does not populate correctly, check the sensor under:

```text
Developer Tools → States
```

Then confirm the exact alert attribute name.

### Temporary Restore Scene ID

The internal scene ID used to snapshot and restore the WLED light.

Default:

```text
before_weather_alerts_wled
```

Most users can leave this unchanged.

### Blink Count

How many times the WLED strip should blink.

Default:

```text
8
```

### Blink Delay

How long to wait between on/off changes.

Default:

```text
500 ms
```

### Flash Brightness

Brightness percentage used while the WLED strip is flashing.

Default:

```text
100%
```

## Behavior

This blueprint triggers when the NWS alert sensor changes state.

It only continues if:

1. The active alert count increased
2. The optional helper is either disabled in the blueprint or turned on
3. At least one alert exists in the alert list attribute

The voice announcement runs whenever those conditions pass.

The WLED flash only runs if the selected occupancy or presence sensor is currently `on`.

## Important Notes

### WLED State Restore

The blueprint uses a temporary Home Assistant scene to snapshot and restore the WLED light state.

This should restore normal on/off state, brightness, and color well.

If your WLED strip was running a complex WLED preset or effect, Home Assistant may not always restore every WLED-specific detail perfectly. For basic colors, brightness, and normal light states, this should work fine.

### Alert Count Increases Only

The blueprint is designed to run only when the number of active alerts increases.

Examples:

```text
0 → 1 = runs
1 → 2 = runs
2 → 1 = does not run
1 → 0 = does not run
```

This prevents announcements when alerts expire or decrease.

### Occupancy Sensor Only Controls WLED Flashing

The occupancy sensor does not block the voice announcement.

This means you can still hear alerts on your selected voice devices even if the office is empty, while avoiding unnecessary desk LED flashing.

## Suggested Dashboard Control

Add the optional helper toggle to your dashboard so you can quickly enable or disable weather alerts.

Example dashboard card:

```yaml
type: tile
entity: input_boolean.weather_alerts
name: Weather Alerts
icon: mdi:alert
```

## Troubleshooting

### No voice announcement

Check that your selected entities are `assist_satellite` entities and that they support announcements.

### No WLED flashing

Check that:

* The WLED light entity is available
* The occupancy sensor is currently `on`
* The automation trace shows the WLED flash section running

### Alert message says only "Weather Alert"

Check the NWS alert sensor attributes in:

```text
Developer Tools → States
```

Look for the alert list attribute and confirm whether the keys are named `Event`, `event`, `Headline`, or `headline`.

### Automation does not run

Check that:

* The NWS alert sensor state actually increased
* The optional helper is on, if enabled
* The selected alert attribute contains at least one alert
* The automation is enabled