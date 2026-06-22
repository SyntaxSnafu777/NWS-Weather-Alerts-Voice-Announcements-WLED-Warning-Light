# HA Weather Beacon

NWS voice announcements and light-based warning alerts for Home Assistant.

A configurable Home Assistant automation blueprint for National Weather Service alerts.

This blueprint announces selected NWS alert types on one or more Home Assistant Voice / Assist satellite devices and can flash warning lights across up to five configurable alert zones, such as WLED LED strips, RGB/RGBW smart bulbs, or basic dimmable smart bulbs.

It supports optional dashboard enable/disable control, alert-type filtering, serious-alert occupancy bypass, optional voice volume control, optional custom preannounce sounds, regular smart bulb compatibility, and automatic light state restore.

## Features

* Announce National Weather Service alerts on one or more Home Assistant Voice / Assist satellites
* Flash Home Assistant `light` entities across up to five alert zones
* Supports WLED strips, RGB/RGBW smart bulbs, and basic dimmable smart bulbs
* Select between alert-color flashing and brightness-only flashing
* Select which alert types should trigger the automation
* Optional helper toggle to enable or disable the automation from a dashboard
* Per-zone occupancy or presence requirement before flashing each zone's lights
* Serious alert bypass options that can flash all enabled zone lights regardless of zone occupancy
* Optional bypass for all enabled alert types
* Optional default or custom preannounce sound before the voice announcement
* Optional mobile push notifications to one or more Home Assistant Companion App devices
* Optional media player volume control for voice alert announcements
* Optional restore of previous voice media player volume
* Automatic restore of previous warning light state
* Configurable blink count, blink speed, and brightness
* Uses different colors for different weather alert categories when alert-color mode is enabled
* Customizable RGB colors for each alert category
* Organized blueprint UI with collapsible sections for advanced options
* Prioritizes active alerts so the most serious enabled alert is announced first
* Runs when the highest-priority enabled alert is new, changed, or becomes more serious
* Optional time-based alert suppression / quiet hours with breakthrough alert types

## Example Use Case

You have Home Assistant Voice devices around your home and a WLED LED strip above your desk.

When a new NWS alert is issued or the highest-priority active alert changes:

1. The selected voice assistants play an optional preannounce sound.
2. The selected voice assistants announce the alert.
3. Optional mobile push notifications are sent to selected phones or tablets.
4. Each enabled alert zone checks its assigned presence sensor.
5. Occupied zones flash their assigned warning lights.
6. If the alert is serious and bypass is enabled, all enabled zone lights flash even if occupancy is not detected.
7. After the alert sequence, the lights return to their previous state.

## Requirements

This blueprint expects the following Home Assistant entities or integrations:

* [NWS Alerts integration sensor](https://github.com/finity69x2/nws_alerts)
* Home Assistant Voice / Assist satellite entity
* One or more Home Assistant `light` entities
* Optional WLED integration
* Optional RGB/RGBW smart bulbs
* Optional basic white/dimmable smart bulbs
* One occupancy or presence binary sensor per enabled alert zone
* Optional `input_boolean` helper toggle
* Optional `media_player` entities for voice volume control
* Optional local media file if using a custom preannounce sound
* Optional Home Assistant Companion App notify entities for push notifications
* Home Assistant 2024.6.0 or newer for blueprint input sections

## Importing the Blueprint into Home Assistant

You can import this blueprint directly into Home Assistant from GitHub.

1. Open Home Assistant.
2. Go to **Settings**.
3. Go to **Automations & scenes**.
4. Select the **Blueprints** tab.
5. Click **Import Blueprint**.
6. Paste this URL:

```text
https://github.com/SyntaxSnafu777/ha-weather-beacon/blob/main/nws_alerts_voice_led.yml
```

7. Click **Preview Blueprint**.
8. Click **Import Blueprint**.
9. After importing, click the blueprint and choose **Create automation**.
10. Fill out the required blueprint inputs for your Home Assistant setup.

Blueprint URL:

```text
https://github.com/SyntaxSnafu777/ha-weather-beacon/blob/main/nws_alerts_voice_led.yml
```

If Home Assistant says the blueprint cannot be found, make sure the GitHub repository is public and that the URL points directly to the blueprint YAML file, not just the repository homepage.

## Blueprint Layout

The blueprint inputs are organized into collapsible sections for easier setup.

The main sections are:

* Core Settings
* Alert Zone 1
* Alert Zone 2
* Alert Zone 3
* Alert Zone 4
* Alert Zone 5
* Enable / Disable Control
* Alert Suppression / Quiet Hours
* Voice, Audio & Preannounce
* Mobile Push Notifications
* Light Behavior
* Alert Type Filters
* Occupancy Bypass
* Alert Colors

Alert Zone 1 is visible by default. Zones 2 through 5 are collapsed by default.

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

When **Light Flash Mode** is set to **Alert colors**, the default warning light colors are:

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

You can override each of these colors in the blueprint settings.

Each alert category has its own RGB color picker input, so users can keep the defaults or customize the warning colors for their own setup.

For example:

```text
Tornado Warning Color: Purple / Magenta
Severe Thunderstorm Warning Color: Red
Flash Flood Warning Color: Blue
Winter / Snow / Ice Color: Cyan
```


## Alert Zones

This blueprint supports up to five alert zones.

Each zone has:

* Enable toggle
* Presence / occupancy sensor
* One or more warning lights

Example setup:

```text
Alert Zone 1:
  Presence Sensor: Office Presence
  Warning Lights: Desk WLED, Office Lamp

Alert Zone 2:
  Presence Sensor: Bedroom Presence
  Warning Lights: Bedroom Lamp

Alert Zone 3:
  Presence Sensor: Garage Motion
  Warning Lights: Garage Light
```

Normal behavior:

```text
Zone has no presence sensor selected = that zone is treated as always occupied
Zone presence detected = that zone's lights flash
Zone presence not detected = that zone's lights do not flash
```

Serious alert bypass behavior:

```text
Bypass enabled for the alert type = all enabled zone lights flash
```

This lets a user have several lights across different areas while still tying each area to its own presence or motion sensor. It also lets a user create a simple always-active light zone by leaving the zone presence sensor blank.

## Light Compatibility

This blueprint is not limited to WLED.

Any Home Assistant `light` entity can be selected as a warning light.

Recommended options:

* WLED strips
* RGB/RGBW Zigbee bulbs
* Hue bulbs
* Kasa/Tapo color bulbs
* Shelly RGBW lights
* Basic white or dimmable smart bulbs

### Light Flash Mode

The blueprint includes a **Light Flash Mode** option.

#### Alert colors - RGB/RGBW lights

Use this mode for WLED strips and color-capable bulbs.

The blueprint sends:

```yaml
brightness_pct: 100
rgb_color: [255, 0, 0]
```

The actual color changes depending on the alert type.

Use this mode when all selected warning lights support RGB color.

#### Brightness only - basic smart bulbs

Use this mode for white-only bulbs, dimmable bulbs, or mixed groups where some lights may not support RGB color.

The blueprint sends brightness only and does not send `rgb_color`.

This avoids errors from integrations that do not support color.

Use this mode for entities such as:

```yaml
light.office_lamp
light.bedroom_lamp
light.basement_smart_bulb
```

The light will blink on and off at the configured brightness, but it will not change alert colors.

### Zones Without Presence Sensors

If an enabled alert zone has lights selected but no presence sensor selected, that zone is treated as always occupied.

This is useful for always-active warning lights, common areas, or users who do not use presence sensors.

## Occupancy Logic

Each enabled alert zone can have its own occupancy or presence sensor.

A presence sensor is optional for each zone.

Normal behavior:

```text
Zone has no presence sensor selected = zone is treated as always occupied
Zone 1 presence detected = Zone 1 lights flash
Zone 2 presence detected = Zone 2 lights flash
Zone 3 presence not detected = Zone 3 lights do not flash
```

This is useful if you want lights in multiple rooms or areas to respond only when someone is actually present in that specific area. It also allows simple always-active zones when no presence sensor is selected.

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

## Alert Suppression / Quiet Hours

The blueprint can suppress alerts during a configured time window, such as overnight or during normal sleeping hours.

During the suppression window, the blueprint only considers alert types that are allowed to break through suppression.

Example:

```text
Use Alert Suppression / Quiet Hours: enabled
Suppression Start Time: 22:00
Suppression End Time: 07:00

Allow Tornado Warning During Suppression: enabled
Allow Flash Flood Warning During Suppression: disabled
Allow Severe Thunderstorm Warning During Suppression: optional
```

With this setup:

```text
10:30 PM Flash Flood Warning = suppressed
10:30 PM Tornado Warning = allowed
10:30 PM Flash Flood Warning + Tornado Warning = Tornado Warning is selected and allowed
```

The suppression window can cross midnight.

Example:

```text
Start: 22:00
End: 07:00
```

This means suppression is active from 10:00 PM through 7:00 AM.

If the start time and end time are the same, suppression is treated as disabled to avoid accidentally suppressing alerts all day.

### Breakthrough Alert Types

Each alert category can be allowed or blocked during the suppression window.

By default, Tornado Warning is allowed to break through suppression. Other alert types are disabled by default during suppression and can be enabled if desired.

Alert suppression affects the whole automation:

* Voice announcements
* Mobile notifications
* Warning lights

If an alert is suppressed, none of those actions run for that alert.

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

## Preannounce Sound

The blueprint can play a preannounce sound before the voice announcement.

You have three options:

```text
Use Alert Suppression / Quiet Hours: optional
Suppression Start Time: 22:00
Suppression End Time: 07:00
Allow Tornado Warning During Suppression: enabled

Use Preannounce Sound: enabled
Custom Preannounce Sound: empty
```

This uses Home Assistant's default Assist satellite preannounce sound.

```text
Use Alert Suppression / Quiet Hours: optional
Suppression Start Time: 22:00
Suppression End Time: 07:00
Allow Tornado Warning During Suppression: enabled

Use Preannounce Sound: enabled
Custom Preannounce Sound: select an audio file from Home Assistant Media
```

This uses your selected custom preannounce sound.

```text
Use Preannounce Sound: disabled
```

This disables the preannounce sound.

### Custom Preannounce Sound

The blueprint uses Home Assistant's media browser selector for custom preannounce audio.

To use a custom sound:

1. Upload a short MP3 or WAV file to Home Assistant Media.
2. In the automation created from this blueprint, open **Voice, Audio & Preannounce**.
3. Enable **Use Preannounce Sound**.
4. Select the file under **Custom Preannounce Sound**.

You do not need to manually type a media source path such as:

```text
media-source://media_source/local/weather-alert-tone.mp3
```

A short MP3 or WAV file is usually best.

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

The blueprint snapshots the enabled alert zone light entities before flashing them.

After the flash sequence, it restores the previous light state for each enabled zone using temporary Home Assistant scenes.

This should restore normal light state, brightness, color, and many standard light attributes.

If your WLED strip was running a complex WLED preset or effect, Home Assistant may not always restore every WLED-specific detail perfectly. For basic light state, color, and brightness, this should work well.

## Alert Priority

If multiple NWS alerts are active at the same time, the blueprint does not simply use the first alert in the NWS alert list.

Instead, it evaluates all active alerts, ignores alert types that are disabled in the blueprint settings, ignores alert types blocked by alert suppression / quiet hours, assigns each remaining matching alert a priority, and uses the highest-priority enabled alert for:

* Voice announcement
* Mobile notification
* Warning light color
* Occupancy bypass logic

Priority order:

| Priority | Alert Type |
|---:|---|
| 1 | Tornado Warning |
| 2 | Flash Flood Warning |
| 3 | Severe Thunderstorm Warning |
| 4 | Tornado Watch |
| 5 | Severe Thunderstorm Watch |
| 6 | Flood Warning |
| 7 | Winter Storm / Blizzard / Ice Warning |
| 8 | High Wind Warning |
| 9 | Excessive Heat Warning |
| 10 | Flood Watch |
| 11 | Winter / Snow / Ice Alerts |
| 12 | Heat Alerts |
| 13 | Wind Alerts |
| 14 | Special Weather Statement |
| 15 | Weather Advisories |
| 16 | Other Alerts |

This helps ensure that if a Severe Thunderstorm Watch and Tornado Warning are active at the same time, the Tornado Warning is the alert that gets announced and used for the warning light color.

## Trigger Behavior

The automation triggers when the NWS alert sensor changes.

The blueprint then compares the current highest-priority enabled alert against the previous highest-priority enabled alert.

It runs when:

```text
There was no previous enabled alert
The highest-priority enabled alert changed
The highest-priority enabled alert became more serious
The highest-priority enabled alert headline/details changed
```

Examples:

```text
No alerts → Tornado Warning = runs
Severe Thunderstorm Watch → Tornado Warning = runs
Severe Thunderstorm Warning + Tornado Warning arrive together = Tornado Warning is used
Tornado Watch + Severe Thunderstorm Warning arrive together = Severe Thunderstorm Warning is used
Tornado Warning remains active and a lower-priority Flood Watch is added = Tornado Warning remains the selected alert
```

There is no cooldown. This avoids blocking a serious alert that appears shortly after a less serious alert.

The blueprint does not announce alerts when the only change is an alert expiring or a lower-priority alert changing while the highest-priority selected alert stays the same.

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

### Alert Suppression / Quiet Hours

Enables optional time-based suppression.

When enabled, only selected alert categories are allowed to break through during the configured time window.

### Suppression Start Time

Time when suppression begins.

Example:

```text
22:00
```

### Suppression End Time

Time when suppression ends.

Example:

```text
07:00
```

### Suppression Breakthrough Alert Types

Each alert category has an option to allow it during the suppression window.

Examples:

```text
Allow Tornado Warning During Suppression: enabled
Allow Flash Flood Warning During Suppression: disabled
Allow Severe Thunderstorm Warning During Suppression: optional
```

### Voice Assist Satellites

One or more Home Assistant Voice / Assist satellite entities that should announce the alert.

### Use Preannounce Sound

Controls whether a sound plays before the voice announcement.

If enabled and the custom media ID is blank, the default Assist satellite preannounce sound is used.

If enabled and the custom media ID is filled in, the custom sound is used.

If disabled, no preannounce sound is played.

### Custom Preannounce Sound

Optional audio file selected from Home Assistant Media.

Leave it empty to use Home Assistant's default Assist satellite preannounce sound.

### Send Mobile Push Notifications

Enables optional push notifications to selected Home Assistant Companion App mobile devices.

### Mobile Notify Entities

One or more notify entities to receive push notifications.

Select one or more notify entities from the entity picker.

Example entity names may look like:

```text
notify.mobile_app_your_phone
notify.mobile_app_another_phone
```

These entities are created by integrations such as the Home Assistant Companion App.

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

### Alert Zones

The blueprint supports up to five alert zones.

Each zone has:

* Enable Alert Zone
* Optional Zone Presence / Occupancy Sensor
* Zone Warning Lights

Enable only the zones you want to use.

If a zone's presence sensor is left blank, that zone is treated as always occupied.

Example:

```text
Enable Alert Zone 1: enabled
Zone 1 Presence / Occupancy Sensor: binary_sensor.office_presence
Zone 1 Warning Lights: light.desk_wled, light.office_lamp

Enable Alert Zone 2: enabled
Zone 2 Presence / Occupancy Sensor: binary_sensor.bedroom_presence
Zone 2 Warning Lights: light.bedroom_lamp
```

Zones with no selected lights will not flash.

### Light Flash Mode

Controls whether the blueprint sends alert colors or brightness only.

Use **Alert colors - RGB/RGBW lights** for WLED and color-capable bulbs.

Use **Brightness only - basic smart bulbs** for white-only bulbs, dimmable bulbs, or mixed groups where not every selected light supports RGB color.

### Alert Color Inputs

Each alert category has a customizable color input.

Available color inputs include:

* Tornado Warning Color
* Tornado Watch Color
* Severe Thunderstorm Warning Color
* Severe Thunderstorm Watch Color
* Flash Flood Warning Color
* Flood Warning / Flood Watch Color
* Winter / Snow / Ice Color
* Heat Alert Color
* Wind Alert Color
* Special Weather Statement Color
* Advisory Color
* Other Alert Color

These are only used when **Light Flash Mode** is set to **Alert colors - RGB/RGBW lights**.

### Temporary Light Restore Scene ID

Base internal scene ID used to snapshot and restore enabled zone warning lights.

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

For most users with WLED or color-capable smart bulbs:

```text
Enable Alert Zone 1: enabled
Zone 1 Presence / Occupancy Sensor: your main room presence sensor
Zone 1 Warning Lights: your WLED strip or color lights

Optional: enable Alert Zones 2-5 for additional rooms or areas

Use Enable Helper: enabled
Enable Helper Entity: input_boolean.weather_alerts

Use Alert Suppression / Quiet Hours: optional
Suppression Start Time: 22:00
Suppression End Time: 07:00
Allow Tornado Warning During Suppression: enabled

Use Preannounce Sound: enabled
Custom Preannounce Sound: empty

Send Mobile Push Notifications: optional
Mobile Notify Entities: select one or more notify entities if enabled

Light Flash Mode: Alert colors - RGB/RGBW lights

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

For basic white smart bulbs:

```text
Light Flash Mode: Brightness only - basic smart bulbs
```

## Testing Without an Active NWS Alert

You can test the blueprint even when there are no active weather alerts by temporarily spoofing the NWS alert sensor from Home Assistant Developer Tools.

This does not permanently change the real NWS integration. Home Assistant will overwrite the test state when the integration updates, reloads, or Home Assistant restarts.

### Before Testing

Make sure the automation created from this blueprint is enabled.

If you enabled the optional helper toggle, make sure it is turned on.

Example:

```yaml
input_boolean.weather_alerts
```

Also make sure your selected voice assistants and warning light entities are available.

### Step 1: Set the NWS Sensor to No Alerts

Go to:

```text
Developer Tools → States
```

Find your NWS alert sensor.

Example:

```yaml
sensor.nws_alerts
```

Set **State** to:

```text
0
```

Set **State attributes** to:

```yaml
Alerts: []
```

Click **Set state**.

This gives the automation a clean starting point.

### Step 2: Trigger a Fake Tornado Warning

Now set the same NWS alert sensor **State** to:

```text
1
```

Set **State attributes** to:

```yaml
Alerts:
  - Event: Tornado Warning
    Headline: TEST ONLY. Tornado Warning test for Home Assistant automation.
    Description: This is only a test of the NWS alert voice and warning light blueprint.
```

Click **Set state**.

The automation should trigger because the alert count changed from:

```text
0 → 1
```

If **Bypass Occupancy for Tornado Warning** is enabled, the warning lights should flash even if your selected occupancy sensor is off.

### Test Other Alert Types

To test other alert categories, first reset the sensor back to `0` with `Alerts: []`, then set it back to `1` with one of the examples below.

#### Severe Thunderstorm Warning

```yaml
Alerts:
  - Event: Severe Thunderstorm Warning
    Headline: TEST ONLY. Severe Thunderstorm Warning test.
    Description: This is only a test.
```

#### Severe Thunderstorm Watch

```yaml
Alerts:
  - Event: Severe Thunderstorm Watch
    Headline: TEST ONLY. Severe Thunderstorm Watch test.
    Description: This is only a test.
```

#### Flash Flood Warning

```yaml
Alerts:
  - Event: Flash Flood Warning
    Headline: TEST ONLY. Flash Flood Warning test.
    Description: This is only a test.
```

#### Flood Watch

```yaml
Alerts:
  - Event: Flood Watch
    Headline: TEST ONLY. Flood Watch test.
    Description: This is only a test.
```

#### Winter Storm Warning

```yaml
Alerts:
  - Event: Winter Storm Warning
    Headline: TEST ONLY. Winter Storm Warning test.
    Description: This is only a test.
```

#### Heat Advisory

```yaml
Alerts:
  - Event: Heat Advisory
    Headline: TEST ONLY. Heat Advisory test.
    Description: This is only a test.
```

#### Special Weather Statement

```yaml
Alerts:
  - Event: Special Weather Statement
    Headline: TEST ONLY. Special Weather Statement test.
    Description: This is only a test.
```

### Testing Alert Suppression / Quiet Hours

To test alert suppression:

1. Enable **Use Alert Suppression / Quiet Hours**.
2. Set the suppression start and end time so the current time is inside the suppression window.
3. Disable **Allow Flash Flood Warning During Suppression**.
4. Run a fake Flash Flood Warning test.

The automation should not run.

Then test breakthrough behavior:

1. Keep the current time inside the suppression window.
2. Enable **Allow Tornado Warning During Suppression**.
3. Run a fake Tornado Warning test.

The automation should run.

To test priority and suppression together, use multiple fake alerts:

```yaml
Alerts:
  - Event: Flash Flood Warning
    Headline: TEST ONLY. Flash Flood Warning test.
  - Event: Tornado Warning
    Headline: TEST ONLY. Tornado Warning test.
```

If Tornado Warning is allowed during suppression and Flash Flood Warning is not, the blueprint should select and run the Tornado Warning.

### Testing Alert Zones

To test alert zones:

1. Enable at least one alert zone.
2. Select one or more lights for that zone.
3. Either leave the zone presence sensor blank, make sure the zone presence sensor is currently `on`, or test with a Tornado Warning while Tornado Warning occupancy bypass is enabled.
4. Run the fake Tornado Warning test.

To test multiple zones, enable more than one zone and use different presence sensors and lights for each zone.

If a zone does not flash, check whether that zone is enabled, has at least one light selected, and either has no presence sensor selected, has presence detected, or is being bypassed by a serious alert.

### Testing Light Flash Modes

To test WLED or RGB bulbs, set **Light Flash Mode** to:

```text
Alert colors - RGB/RGBW lights
```

Then test a few different alert types. The light color should change depending on the alert.

To test basic smart bulbs, set **Light Flash Mode** to:

```text
Brightness only - basic smart bulbs
```

Then run the same fake alert test. The selected lights should blink on and off without changing color.

Use brightness-only mode if your automation trace shows an error related to `rgb_color` or unsupported color features.

### Testing Custom Preannounce Sounds

To test a custom preannounce sound:

1. Upload a short MP3 or WAV file to Home Assistant Media.
2. Enable **Use Preannounce Sound**.
3. Select the audio file under **Custom Preannounce Sound**.
4. Run the fake Tornado Warning test.

If the custom sound does not play, clear **Custom Preannounce Sound** and test again. With no custom sound selected, Home Assistant should use the default Assist satellite preannounce sound.

### Testing Mobile Push Notifications

To test mobile push notifications:

1. Enable **Send Mobile Push Notifications**.
2. Select at least one notify entity under **Mobile Notify Entities**.
3. Run the fake Tornado Warning test.

You should receive a Companion App push notification with the alert event as the title and the alert headline as the message.

If the notification does not arrive, confirm that the selected notify entity exists and the device is connected through the Home Assistant Companion App and that notification permissions are allowed on the mobile device.

### Testing Custom Alert Colors

To test custom colors:

1. Set **Light Flash Mode** to **Alert colors - RGB/RGBW lights**.
2. Change one of the alert color inputs, such as **Tornado Warning Color**.
3. Run the fake Tornado Warning test.
4. Confirm the selected warning lights use your custom color.

Custom colors are not used when **Light Flash Mode** is set to **Brightness only - basic smart bulbs**.

### Reset After Testing

After testing, set the NWS alert sensor back to:

```text
0
```

with attributes:

```yaml
Alerts: []
```

You can also reload the NWS Alerts integration or restart Home Assistant to let the real integration state take back over.

### Testing Notes

The blueprint now uses highest-priority alert change detection instead of simple alert-count-only detection.

For repeated tests, reset the sensor to `0` with `Alerts: []`, then set it back to `1` with the fake alert you want to test.

To test priority behavior, you can include multiple fake alerts at the same time.

Example:

```yaml
Alerts:
  - Event: Severe Thunderstorm Watch
    Headline: TEST ONLY. Severe Thunderstorm Watch test.
  - Event: Tornado Warning
    Headline: TEST ONLY. Tornado Warning test.
```

The blueprint should choose the Tornado Warning because it has higher priority.

### If the Test Does Not Work

Open the automation and check **Traces**.

Common causes:

* The optional helper toggle is off
* The selected test alert type is disabled in the blueprint settings
* The NWS alert attribute name is not set correctly
* The automation did not see the state increase from `0` to `1`
* The selected Assist satellite entity is unavailable
* The selected zone warning light entity is unavailable
* The occupancy sensor is off and occupancy bypass is not enabled for that alert type
* Light Flash Mode is set to alert colors but one of the selected lights does not support RGB color
* The selected custom preannounce sound is invalid or inaccessible

For the easiest first test, use **Tornado Warning** because the blueprint defaults to bypassing occupancy for Tornado Warnings.

## Troubleshooting

### Alert is suppressed during quiet hours

Check that:

* **Use Alert Suppression / Quiet Hours** is configured as intended
* The current time is outside the suppression window, or
* The alert type is enabled as a breakthrough alert during suppression

If the current time is inside the suppression window and the alert type is not allowed to break through, the automation will not run.

### No voice announcement

Check that:

* You selected `assist_satellite` entities
* The Assist satellite entities are available
* The automation trace shows the announcement action running

### Preannounce sound does not play

Check that:

* **Use Preannounce Sound** is enabled
* The selected Assist satellite supports preannounce audio
* If using a custom sound, the selected **Custom Preannounce Sound** is valid and accessible

Try clearing **Custom Preannounce Sound**. If the default preannounce sound works, the issue is likely the selected media file.

### Import/save error: Unknown device `{{ repeat.item }}`

Older versions of this blueprint used a templated device action for mobile notifications.

The current version avoids that problem by using selectable `notify` entities with `notify.send_message`.

### Mobile notification does not arrive

Check that:

* **Send Mobile Push Notifications** is enabled
* At least one notify entity is selected under **Mobile Notify Entities**
* The selected notify entity exists and belongs to a device registered through the Home Assistant Companion App
* Notifications are allowed on the mobile device
* The automation trace shows the mobile notification section running

### Voice volume does not change

Check that:

* `Set Voice Alert Volume` is enabled
* You selected the related `media_player` entities
* Your voice assistant exposes a media player entity that supports volume control

The Assist satellite entity handles announcements, while the media player entity is only used for volume.

### No light flashing

Check that:

* The selected zone light entities are available
* The zone occupancy or presence sensor is `on`, or no zone presence sensor is selected
* Or the alert type is configured to bypass occupancy
* The automation trace shows the zone light flashing section running

### Custom alert color does not apply

Check that:

* **Light Flash Mode** is set to **Alert colors - RGB/RGBW lights**
* You changed the color for the correct alert category
* The selected light supports RGB color
* The selected light integration accepts `rgb_color`

### Light color does not change

Check that:

* **Light Flash Mode** is set to **Alert colors - RGB/RGBW lights**
* The selected light supports RGB color
* The selected light integration accepts `rgb_color`

If the light is a basic white or dimmable bulb, use **Brightness only - basic smart bulbs**.

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

* The highest-priority enabled alert is new, changed, or more serious than the previous selected alert
* Your selected alert type is enabled in the blueprint
* Alert suppression / quiet hours is not blocking the selected alert type
* The optional helper is on, if enabled
* The alert list attribute contains at least one active alert
* The automation is enabled

## Notes

Because this blueprint uses input sections, it requires Home Assistant 2024.6.0 or newer.

This blueprint intentionally prioritizes active alerts and uses the highest-priority enabled alert for announcements, notifications, light colors, and bypass logic.

It does not use a cooldown, so a more serious alert can still trigger immediately after a less serious alert.

Alert suppression / quiet hours is applied before priority selection. During suppression, only alert types allowed to break through are considered.

It does not re-announce when alerts expire or when the highest-priority selected alert remains unchanged.