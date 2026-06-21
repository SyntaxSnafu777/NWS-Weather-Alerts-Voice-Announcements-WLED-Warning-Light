# NWS Weather Alerts - Voice Announcements & Warning Lights

A configurable Home Assistant automation blueprint for National Weather Service alerts.

This blueprint announces selected NWS alert types on one or more Home Assistant Voice / Assist satellite devices and can flash one or more RGB/RGBW warning lights, such as WLED LED strips.

It supports optional dashboard enable/disable control, alert-type filtering, serious-alert occupancy bypass, optional voice volume control, and automatic light state restore.

## Features

* Announce National Weather Service alerts on one or more Home Assistant Voice / Assist satellites
* Flash one or more RGB/RGBW light entities, including WLED strips
* Select which alert types should trigger the automation
* Optional helper toggle to enable or disable the automation from a dashboard
* Optional occupancy or presence requirement before flashing lights
* Serious alert bypass options for Tornado Warning, Severe Thunderstorm Warning, and Flash Flood Warning
* Optional bypass for all enabled alert types
* Optional media player volume control for voice alert announcements
* Optional restore of previous voice media player volume
* Automatic restore of previous warning light state
* Configurable blink count, blink speed, and brightness
* Uses different colors for different weather alert categories
* Only triggers when the NWS alert count increases

## Example Use Case

You have Home Assistant Voice devices around your home and a WLED LED strip above your desk.

When a new NWS alert is issued:

1. The selected voice assistants announce the alert.
2. The selected WLED light strips flash in an alert-specific color.
3. If the alert is not serious, the lights only flash when your selected occupancy sensor is active.
4. If the alert is serious and bypass is enabled, the lights flash even if occupancy is not detected.
5. After the alert sequence, the lights return to their previous state.

## Requirements

This blueprint expects the following Home Assistant entities or integrations:

* NWS Alerts integration sensor
* Home Assistant Voice / Assist satellite entity
* One or more RGB/RGBW light entities
* Optional WLED integration
* Occupancy or presence binary sensor
* Optional `input_boolean` helper toggle
* Optional `media_player` entities for voice volume control

## Recommended Helper Toggle

Create a toggle helper if you want to enable or disable weather alert automations from your dashboard.

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

When the helper is on, alerts can run.

When the helper is off, the automation is blocked.

## Alert Types

The blueprint lets you choose which alert categories should trigger the automation.

Available options include:

* Tornado Warning
* Tornado Watch
* Severe Thunderstorm Warning
* Severe Thunderstorm Watch
* Flash Flood Warning
* Flood Warning
* Flood Watch
* Winter / Snow / Ice Alerts
* Heat Alerts
* Wind Alerts
* Special Weather Statement
* Weather Advisories
* Other Alerts

By default, the more important watch and warning categories are enabled. Lower-priority categories such as Special Weather Statement and advisories are disabled by default.

## Alert Colors

Default warning light colors:

| Alert Type                     | Color             |
| ------------------------------ | ----------------- |
| Tornado Warning                | Magenta / Purple  |
| Tornado Watch                  | Orange            |
| Severe Thunderstorm Warning    | Red               |
| Severe Thunderstorm Watch      | Yellow            |
| Flash Flood Warning            | Blue              |
| Flood Warning / Flood Watch    | Blue              |
| Winter / Snow / Ice / Blizzard | Cyan              |
| Heat / Excessive Heat          | Orange            |
| Wind / High Wind               | Pale blue / white |
| Special Weather Statement      | White             |
| Advisory                       | White             |
| Other Alert                    | White             |

## Occupancy Logic

The selected occupancy or presence sensor controls whether the warning lights flash.

Normal behavior:

```text
Occupancy detected = lights flash
Occupancy not detected = voice announcement only
```

This is useful if you only want your desk lights, office lights, or bedroom LEDs to flash when someone is actually in that room.

## Serious Alert Occupancy Bypass

Some alerts are serious enough that you may want the lights to flash regardless of occupancy.

The blueprint includes bypass options for:

* Tornado Warning
* Severe Thunderstorm Warning
* Flash Flood Warning
* All enabled alerts

Recommended settings:

```text
Bypass Occupancy for Tornado Warning: enabled
Bypass Occupancy for Severe Thunderstorm Warning: optional
Bypass Occupancy for Flash Flood Warning: optional
Bypass Occupancy for All Enabled Alerts: disabled
```

## Voice Announcements

The blueprint uses Home Assistant Voice / Assist satellite entities for announcements.

You can select multiple Assist satellite entities.

Example entities:

```yaml
assist_satellite.office_voice
assist_satellite.living_room_voice
assist_satellite.bedroom_voice
```

The announcement format is:

```text
National Weather Service alert. [Alert Event]. [Alert Headline].
```

## Voice Volume Control

Home Assistant Voice / Assist satellite entities do not always directly expose volume controls.

For that reason, this blueprint has a separate optional input for `media_player` entities.

If your voice assistant also exposes a `media_player` entity, you can enable volume control and select the related media player entity.

Example:

```text
Set Voice Alert Volume: enabled
Voice Media Player Entities: media_player.office_voice
Voice Alert Volume: 0.8
Restore Voice Volume After Alert: enabled
```

The Assist satellite entity handles the announcement.

The media player entity is only used for volume control.

If your voice assistant does not expose a media player entity, leave volume control disabled.

## Light Restore Behavior

The blueprint snapshots the selected warning light entities before flashing them.

After the flash sequence, it restores the previous light state using a temporary Home Assistant scene.

This should restore normal light state, brightness, and color.

If your WLED strip was running a complex WLED preset or effect, Home Assistant may not always restore every WLED-specific detail perfectly. For basic light state, color, and brightness, this should work well.

## Trigger Behavior

The automation triggers when the NWS alert sensor changes state.

It only runs when the alert count increases.

Examples:

```text
0 → 1 = runs
1 → 2 = runs
2 → 1 = does not run
1 → 0 = does not run
```

This prevents the automation from announcing alerts when alerts expire or decrease.

## Blueprint Inputs

### NWS Alert Sensor

The NWS Alerts sensor whose state is the active alert count.

Example:

```yaml
sensor.nws_alerts
```

### Alert List Attribute

The attribute on the NWS sensor containing the list of active alerts.

For many NWS Alerts integrations, this is:

```text
Alerts
```

If alert details are not populating correctly, check:

```text
Developer Tools → States → your NWS alert sensor
```

Look at the exact attribute name and update this input if needed.

### Use Enable Helper

Enables or disables the optional helper toggle logic.

### Enable Helper Entity

Optional `input_boolean` helper used as a master enable/disable switch.

Example:

```yaml
input_boolean.weather_alerts
```

### Voice Assist Satellites

One or more Home Assistant Voice / Assist satellite entities that should announce the alert.

### Set Voice Alert Volume

Enables optional volume control using selected `media_player` entities.

### Voice Media Player Entities

Optional `media_player` entities associated with your voice assistant devices.

These are used only for volume control.

### Voice Alert Volume

Volume level used before the announcement when volume control is enabled.

Example values:

```text
0.5 = 50%
0.8 = 80%
1.0 = 100%
```

### Restore Voice Volume After Alert

Attempts to restore the previous media player volume after the alert announcement.

### Occupancy / Presence Sensor

Binary sensor used to decide whether lights should flash.

Example entities:

```yaml
binary_sensor.office_occupancy
binary_sensor.office_presence
binary_sensor.desk_motion
```

### Warning Light Entities

One or more light entities to flash during the alert.

Example entities:

```yaml
light.desk_wled
light.office_led_strip
light.bedroom_warning_light
```

### Temporary Light Restore Scene ID

Internal scene ID used to snapshot and restore selected warning lights.

Default:

```text
before_weather_alert_lights
```

Most users can leave this unchanged.

### Temporary Volume Restore Scene ID

Internal scene ID used to snapshot and restore selected media player volume states.

Default:

```text
before_weather_alert_voice_volume
```

Most users can leave this unchanged.

### Blink Count

Number of on/off blinks.

Default:

```text
8
```

### Blink Delay

Delay between each on/off change.

Default:

```text
500 ms
```

### Flash Brightness

Brightness percentage used while the lights flash.

Default:

```text
100%
```

## Suggested Dashboard Card

Add the optional helper toggle to your dashboard.

Example tile card:

```yaml
type: tile
entity: input_boolean.weather_alerts
name: Weather Alerts
icon: mdi:alert
```

## Recommended Starting Configuration

For most users, a good starting point is:

```text
Use Enable Helper: enabled
Enable Helper Entity: input_boolean.weather_alerts

Trigger on Tornado Warning: enabled
Trigger on Tornado Watch: enabled
Trigger on Severe Thunderstorm Warning: enabled
Trigger on Severe Thunderstorm Watch: enabled
Trigger on Flash Flood Warning: enabled
Trigger on Flood Warning: enabled
Trigger on Flood Watch: enabled
Trigger on Winter / Snow / Ice Alerts: enabled
Trigger on Heat Alerts: enabled
Trigger on Wind Alerts: enabled
Trigger on Special Weather Statement: disabled
Trigger on Weather Advisories: disabled
Trigger on Other Alerts: disabled

Bypass Occupancy for Tornado Warning: enabled
Bypass Occupancy for Severe Thunderstorm Warning: optional
Bypass Occupancy for Flash Flood Warning: optional
Bypass Occupancy for All Enabled Alerts: disabled

Set Voice Alert Volume: optional
Restore Voice Volume After Alert: enabled
Blink Count: 8
Blink Delay: 500 ms
Flash Brightness: 100%
```

## Troubleshooting

### No voice announcement

Check that:

* You selected `assist_satellite` entities
* The Assist satellite entities are available
* The automation trace shows the announcement action running

### Voice volume does not change

Check that:

* `Set Voice Alert Volume` is enabled
* You selected the related `media_player` entities
* Your voice assistant exposes a media player entity that supports volume control

The Assist satellite entity handles announcements, while the media player entity is only used for volume.

### No light flashing

Check that:

* The selected light entities are available
* The occupancy sensor is `on`
* Or the alert type is configured to bypass occupancy
* The automation trace shows the light flashing section running

### Lights do not restore exactly

Home Assistant scenes should restore normal light state, brightness, and color.

Complex WLED presets or effects may not always restore perfectly depending on how the WLED entity exposes state to Home Assistant.

### Alert message only says generic weather alert text

Check your NWS alert sensor attributes in:

```text
Developer Tools → States
```

Confirm that the alert list attribute is actually named:

```text
Alerts
```

If your integration uses a different attribute name, update the blueprint input named **Alert List Attribute**.

### Automation does not run during an alert

Check that:

* The NWS alert sensor state increased
* Your selected alert type is enabled in the blueprint
* The optional helper is on, if enabled
* The alert list attribute contains at least one active alert
* The automation is enabled

## Notes

This blueprint intentionally only runs when the alert count increases.

It does not re-announce when alerts expire or when the count decreases.

If multiple alerts are active, the blueprint announces the first active alert that matches your enabled alert categories.
