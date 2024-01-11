# Home Assistant integration for SenseME fans HACS Edition

[![Github version](https://img.shields.io/github/v/release/mikeperry/senseme-hacs)](https://github.com/mikeperry/senseme-hacs/releases/latest) [![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg)](https://hacs.xyz/)

***Note: SenseME integration is becoming a part of Home Assistant. Stay tuned for updates!***

The Haiku with SenseME fan is a WiFi connected fan and optional light from Big Ass Fans. This Home Assistant integration provides control of these fans and light. The occupancy sensor is also monitored. BAF made a standalone light for a while that is also compatible with this integration.

Now [aiosenseme](https://pypi.org/project/aiosenseme/) is the underlying library. It is asynchronous and fits well with Home Assistant. There are several key new features like automatic fan discovery and push updates. It keeps a socket open to each fan added to Home Assistant for push updates and commands like turn fan on. The single socket approach seems to cause fewer issues with loss of connection or the fan going dumb for while. BAF made a standalone light for a while that is also compatible with this integration.


## Installation

### HACS

If you have HACS installed on Home Assistant then just search integrations for **SenseME** and install.

### Manual

Copy the custom_components folder of this repository to your config folder and restart Home Assistant.

## Configuration

1. Go to **Configuration -> Integrations**.
2. Click on the **+ ADD INTEGRATION** button in the bottom right corner.
3. Search for and select the `SenseME` integration.

   <img src="img/search.png"/>

4. If any devices are discovered you will see the dialog below. Select a discovered device and click `Submit` and you are done. If you would prefer to add a device by IP address select that option, click `Submit`, and you will be presented with the dialog in step 5.

   <img src="img/device.png"/>

5. If no devices were discovered or you selected the `IP Address` option the dialog below is presented. Here you can type in an IP address of undiscoverable devices.

   <img src="img/address.png"/>

6. Repeat these steps for each device you wish to add.

## Using the SenseME integration

The SenseME integration supports speed and direction for fans. Whoosh and Sleep are handled by preset modes for fans. If your fan has a light installed it will automatically be detected and added to Home Assistant. All SenseME lights support 16 levels of brightness.

If the device has an occupancy sensor it is also added to Home Assistant but I have not looked at how well it performs. As far as I can tell there are no settings/adjustments for this sensor so what you see is what you get.

The discontinued Haiku standalone lights are also supported, including color temperature.

The Haiku by BAF app supports grouping devices into rooms. Changing any device in a room changes all devices in that room. So if you grouped devices you can get away with added only one of those devices to Home Assistant.

## SenseME platforms

When the integration connects to a device it retrieves the *Device Name* you set in the Haiku by BAF app and uses that as a prefix for all created entities.

* For fans you get the following platforms:
  * `fan` named "*Device Name*".
    * Supports On/Off.
    * Supports Speed percentage that snaps to possible speeds, usually 7 not including off.
    * Supports Directions Forward and Reverse.
    * Supports Preset Mode Whoosh.
  * `light` (if it exists) named "*Device Name* Light".
    * Supports Brightness percentage that snaps to possible levels usually 16 not including off.
  * `binary_sensor` (if it exists) named "*Device Name* Occupancy".
    * Current occupancy status.
    * Device class is occupancy.
  * `switch` named "*Device Name* Sleep Mode".
    * Enable/disable sleep mode in fan.
  * `switch` named "*Device Name* Motion".
    * Enable/disable automatic control of fan based on occupancy.
  * `switch` (if it exists) named "*Device Name* Light Motion".
    * Enable/disable automatic control of fan's light based on occupancy.
* For lights you get the following platforms:
  * `light` named "*Device Name*".
    * Supports Brightness percentage that snaps to possible light brightness levels usually 16 not including off.
    * Supports Color Temp.
  * `binary_sensor` named "*Device Name* Occupancy".
    * Current occupancy status.
    * Device class is occupancy.
  * `switch` named "*Device Name* Sleep Mode".
    * Enable/disable sleep mode in light.
  * `switch` named "*Device Name* Motion".
    * Enable/disable automatic control of light based on occupancy.

## SenseME platform attributes

* All platforms: (fan, light, binary_sensor and sensor)
  * `room_name`: When the device is associated in a group of devices this will be the name of the room. All devices in the group will have the same name for `room_name`. `room_name` will be *"EMPTY* if the device is not in a room.
  * `room_type`: When the device is associated in a group of devices this will be the type of room. All fans in the group will have the same `room_type`. There 29 room types: *"Undefined"*, *"Other"*, *"Master Bedroom"*, *"Bedroom"*, *"Den"*, *"Family Room"*, *"Living Room"*, *"Kids Room"*, *"Kitchen"*, *"Dining Room"*, *"Basement"*, *"Office"*, *"Patio"*, *"Porch"*, *"Hallway"*, *"Entryway"*, *"Bathroom"*, *"Laundry"*, *"Stairs"*, *"Closet"*, *"Sunroom"*, *"Media Room"*, *"Gym"*, *"Garage"*, *"Outside"*, *"Loft"*, *"Playroom"*, *"Pantry"* and *"Mudroom"*
* Fan platform
  * `auto_comfort`: Auto Comfort allows the fan to monitor and adjust to room conditions like temperature, humidity and occupancy. There are four possible states: *"Off"*, *"Cooling"*, *"Heating"*, and *"Followtstat"*.
  * `smartmode`: Smartmode indicates the fan's comfort mode. When `auto_comfort` is set to *"Followtstat"* the actual `auto_comfort` value will change based the connected thermostat otherwise `smartmode` tracks `auto_comfort`. There are three possible states: *"Off"*, *"Cooling"* and *"Heating"*.

## SenseME integration options

There are no options for the SenseME integration.

## Breaking Changes

* From v2.2.0 on discovery is only used in the initial setup of devices. After initial setup the IP address is used connect to devices. If the IP address changes for any reason you will need to add the device again.
* From v2.0.0 on this integration is configured via the Home Assistant frontend only and will no longer allow configuration via YAML. You need to remove the ```senseme:``` section from your configuration file to eliminate the error that pops up each time you restart.

## Issues

* This integration is currently NOT compatible with the [i6 fan](https://www.bigassfans.com/fans/i6/). This probably applies to the [es6 fan](https://www.bigassfans.com/fans/es6/) as well but it has not been tested.
* Unknown models will produce a warning 'Discovered unknown SenseME device model' in Home Assistant. If you get this warning post an issue on [GitHub](https://github.com/mikelawrence/senseme-hacs/issues) with the model detected and I'll add that model to stop the warning.
* The occupancy sensor is treated differently than other devices settings/states; occupancy state changes are not pushed immediately and must be detected with periodic status updates. This will make updates to the occupancy sensor sluggish. This sensor in my two fans is a bit erratic. They tend to detect occupancy when there is no one present including pets.
* Sometimes SenseME devices just don't respond to discovery packets. If you are trying to add the device you can simply use the IP address or you can try a again later when the SenseME devices are more cooperative.

## Debugging

To aid in debugging you can add the following to your configuration.yaml file. Be sure not to duplicate the ```logger:``` section.

```yaml
logger:
  default: warning
  logs:
    custom_components.senseme: debug
    aiosenseme.device: debug
    aiosenseme.discovery: debug
```
