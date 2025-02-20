![Version](https://img.shields.io/github/v/release/skodaconnect/homeassistant-myskoda)
![Downloads](https://img.shields.io/github/downloads/skodaconnect/homeassistant-myskoda/total)
![Contributors](https://img.shields.io/github/contributors/skodaconnect/homeassistant-myskoda)
![Translation](https://badges.crowdin.net/homeassistant-myskoda/localized.svg)

# MySkoda Integration for Skoda vehicles compatible with MySkoda-App.

MySkoda integration using the API that the MySkoda App uses.

**Status:** This project is a new implementation of a relatively new API. Consider this code alpha.

Contributions are welcomed, both as issues, but more as pull requests :)

Please [join our Discord](https://discord.gg/t7az2hSJXq) and help development by providing feedback and details about the vehicles you are using.

## Translations

You can help us translate your MySkoda integration into your language!
Just [join our Crowdin project](https://crowdin.com/project/homeassistant-myskoda/invite?h=1c4f8152c707b666f570b9cb68678ece2227331)
If your desired language is not available, please [open an issue](https://github.com/skodaconnect/homeassistant-myskoda/issues/new/choose) and let us know about it!

## Capabilities

### Sensors

- Software Version
- Battery Percentage
- Charging Power
- Remaining Distance
- Target Battery Percentage
- Milage
- Charge Type
- Charging State
- Remaining Charging Time
- Last Updated
- Maintenance Interval
- Oil Service Interval
- Fuel Level
- Combustion range
- Electric range

### Binary Sensors

- Charger Connected
- Charger Locked
- Everything Locked
- Doors Locked
- Doors Open
- Windows Open
- Trunk Open
- Bonnet Open
- Lights On

### Switches

- Window Heating
- Reduced Current
- Battery Care Mode
- Doors

### Buttons
- Honk and Flash
- Flash

### Climate

- Air conditioning is exposed as climate.
- Auxiliary heater is exposed as climate.

### Numbers

- Charging Limit

### Device Tracker

Location of vehicles are exposed as device trackers.

## Sending updates

To make sure updates end up in the car the way intended by the you, we throttle all equal changes to the car for 30s.
Simply put: You can change a setting in the car once every 30s, but you can change multiple settings in sequence.

So:
- If you change the seat-heating to on, you can immediately change the window-heating to on as well.
- If you change the seat-heating to on, you cannot turn it back off again for 30s

The requests will silently be ignored by HomeAssistant, so make sure you wait at least 30s before sending another request

## Installation
You can manually install this integration as an custom_component under Home Assistant or install it using HACS (Home Assistant Community Store).

### Manual installation
1. **Download** the `myskoda` repository or folder.
2. **Copy** the `custom_components/myskoda` folder from the downloaded files.
3. **Paste** the `myskoda` folder into your Home Assistant's custom components directory:
   - Path: `<home_assistant_folder>/custom_components/myskoda`
4. **Restart** Home Assistant to load the new integration.

### HACS installation
The `myskoda` repository is also compatible with HACS (Home Assistant Community Store), making installation and updates easier.

1. **Install HACS** (if not already installed):
   - Follow instructions here: [HACS Installation Guide](https://hacs.xyz/docs/installation/manual)
2. **Add `myskoda` Repository** to HACS:
   - In Home Assistant, go to **HACS** > **Settings** tab.
   - Select **Custom Repositories** and add the repository URL `https://github.com/skodaconnect/homeassistant-myskoda`.
3. **Install `myskoda`** from HACS:
   - After adding the repository, find and install `myskoda` under the HACS integrations.
4. **Restart** Home Assistant.

Following these steps should successfully install the `myskoda` integration for use with your Home Assistant setup.

For more guidance on HACS, you can refer to the [HACS Getting Started Guide](https://hacs.xyz/docs/basic/getting_started).

## Enable debug logging

For comprehensive debug logging you can add this to your `<config dir>/configuration.yaml`:

```yaml
logger:
  default: info
  logs:
    myskoda: debug
    myskoda.mqtt: debug
    myskoda.rest_api: debug
    custom_components.myskoda: debug
    custom_components.myskoda.climate: debug
    custom_components.myskoda.lock: debug
    custom_components.myskoda.device_tracker: debug
    custom_components.myskoda.switch: debug
    custom_components.myskoda.binary_sensor: debug
    custom_components.myskoda.sensor: debug
    custom_components.myskoda.number: debug
    custom_components.myskoda.image: debug
 ```

Pick any of the subjects from the example. If you want to enable full debugging, you only need **myskoda** and **custom_components.myskoda**.

- **myskoda:** Set the debug level for the MySkoda library. This handles all the communication with MySkoda.

- **myskoda.mqtt:** Set the debug level for the MQTT class of the MySkoda library. This handles the push-messages sent from MySkoda to the integration, notifying of changes to/in the car.

- **myskoda.rest_api:** Set the debug level for the REST API of the MySkoda library. This handles all the pull-messages the integration fetches from MySkoda.

- **custom_components.myskoda:** Set debug level for the custom component. The communication between hass and library.

- **custom_components.myskoda.XYZ** Sets debug level for individual entity types in the custom component.

## Customize polling interval

This integration does not poll at a set interval, instead when the last update has been a while, we request new information from MySkoda.
The reason for this is that cars emit a lot of events when they are operating, and we use these events to partially update the car information.

When the car is quiet for a period we call the **POLLING INTERVAL**, we will request a full update of the car.

By default, this POLLING INTERVAL is set to 30 minutes. You can tune this to anything between 1 and 1440 minutes (1 day) by filling in the desired value in
Integrations > MySkoda > Hubs > Select your account > Configure

## Disabling polling

You can disable polling completely and use automations to update the data from MySkoda. In order to do this, disable polling in the integration, and call the following action:

```yaml
action: homeassistant.update_entity
target:
  entity_id: device_tracker.skoda_4ever
```

## S-PIN

For some operations, such as locking and unlocking doors, it is required to fill in the S-PIN that you have set for privileged access to your car via the App. This integration does not support setting the S-PIN yet.
Fill in the required S-PIN in Settings > Integrations > MySkoda > Configuration > S-PIN and after a few seconds you will have the options available in HomeAssistant.

## Read-only mode

The opposite to S-PIN is read-only mode. In this mode, all buttons, switches and other functionality that allows you to change settings remotely are disabled.
In order not to accidentally delete data, we do not delete the entities

Also, if you disable read-only mode, the buttons, switches, etc will become available again.

## Disclaimer

This Homeassistant integration uses an unofficial API client for the Skoda API and is not affiliated with, endorsed by, or associated with Skoda Auto or any of its subsidiaries.

Use this project at your own risk. Skoda Auto may update or modify its API without notice, which could render this integration inoperative or non-compliant. The maintainers of this project are not responsible for any misuse, legal implications, or damages arising from its use.

Ensure compliance with Skoda Auto's terms of service and any applicable laws when using this software.
