Documentation of the TinkerForge binding bundle

## Introduction

[TinkerForge](http://www.tinkerforge.com) is a system of open source hardware building blocks that
allows you to combine sensor and actuator blocks by plug and play. You can create your individual
hardware system by choosing the necessary building blocks for your project and combine it with other
home automation products. There are many blocks available e.g for temperature, humidity or air pressure
measurement as well as for I/O, LCDs and motor control. You will find a complete List of available
blocks [here](http://www.tinkerforge.com/en/doc/Product_Overview.html).

This binding connects the [TinkerForge](http://tinkerforge.com) devices to the openHAB event bus.
Sensor values from devices are made available to openHAB and actions on  devices can be triggered by
openHAB.

The following devices are supported:
- Servo Brick
- DC Brick
- Dual Relay Bricklet
- Humidity Bricklet
- Distance IR Bricklet
- Temperature Bricklet
- Barometer Bricklet (barometer and temperature device)
- Ambient Light Bricklet
- LCD 20×4 Bricklet (LCD, backlight and 4 buttons)
- Industrial Quad Relay
- Industrial Digital In 4
- IO-16
- Remote Switch
- Motion Detector
- MultiTouch
- TemperatureIR
- SoundIntensity
- Moisture
- DistanceUS
- VoltageCurrent
- Tilt
- IO-4
- Industrial Digital Out 4
- Segment Display 4x7
- LED Strip
- Joystick
- Linear Poti
- Dual Button
- PTC
- Industrial Dual 0-20mA
- Solid State Relay

The TinkerForge binding bundle is available as a separate (optional) download. For installation of
the binding, please see Wiki page [[Bindings]].

## Upgrading from 1.3
- LCDBacklight now is a sub device of LCD20x4 Bricklet (items file has to be changed)
- LCD20x4Button now posts an update not a command anymore (rules has to be changed)
- IndustrialQuadRelay sub id numbering now starts from zero (items file has to be changed)

## Upgrading from 1.4
* Threshold values now have the same unit as the sensor value (incompatible change, you may have to update your openhab.cfg).
   * Background:
     Some getters for sensor values of the TinkerForge API return higher precision values by using short values with fractions of the common units, e.g. the Temperature Bricklet returns hundredths of a celsius degree. The binding converts these values to common units using a BigDecimal. Until now the threshold values were applied to the sensor value before this conversion. Because of that the threshold values had to be given as the appropriate fraction. With the drawback that the openHAB users need some knowledge about the behavior of the TinkerForge API. Now the threshold is applied after converting the original values. Therefore the units used for the sensor values and the threshold values are equal.
    * Humidity Bricklet
       * calculate new threshold values from values of your current configuration: divide by 10
       * unity: relative humidity in percent
    * Distance IR Bricklet
       * calculate new threshold values from values of your current configuration: nothing changed
       * unity: millimeter
    * Temperature Bricklet
       * calculate new threshold values from values of your current configuration: divide by 100
       * unity: degree Celsius
    * Barometer Bricklet
       * calculate new threshold values from values of your current configuration: divide by 1000
       * unity: mbar
    * Ambient Light Bricklet
       * calculate new threshold values from values of your current configuration: divide 10
       * unity: Lux

## 1.7 New & Noteworthy
### New Devices
 * Joystick Bricklet
 * Linear Poti Bricklet
 * Dual Button Bricklet
 * PTC
 * Industrial Dual 0-20mA
 * Solid State Relay

### New Features
 * Tinkerforge Action Addon
 * Brick DC fully supported
 * Brick Servo fully supported
 * Authentication support for brickd
 * Tactile feature for Dualbutton, LCD Buttons, Joystick Button.
 * LED Strip: sub devices and switching capabilities, configurable Frameduration, ChipType and Clockfrequency
 * Remote Switch dimmer support

### Bugfixes
* Fix for configuration handling of device aliases

### Other changes
 * Updated Tinkerforge API to 2.1.4
 * [Example configurations](https://github.com/theoweiss/openhab-tinkerforge-configuration-examples) available on github

### Brick DC
#### Incompatible changes
* DriveMode now is one of "brake" or "coast" instead of "0" or "1"
```
tinkerforge:dc_garage.driveMode=brake
```

* switchOnVelocity in openhab.cfg is no longer needed and has gone.
It is replaced by per item configuration:
With the benefit that you can have serveral switch items with different speeds.
~~tinkerforge:dc_garage.switchOnVelocity=10000~~
```
Switch DCSWITCH "DC Switch" {tinkerforge="uid=62Zduj, speed=14000"}
```

#### Whats new?
Support for Dimmer, Rollershuter and Number items. Besides that the speed
can be set using a percent value.

The number items show the current velocity. The values are reported using the VelocityListener.
"callbackPeriod" and "threshold" for the listener can be configured in openhab.cfg. There is more
documentation about callback listeners at the official openHAB TinkerForgeBindig wiki page.

* callbackPeriod: milliseconds
* threshold: numeric value

#### New item configuration options
* speed: the target speed (Switch)
* max: the maximum speed (Dimmer, Rollershutter)
* min: the minimum speed (Dimmer, Rollershutter)
* step: the step value for increasing decreasing speed (Dimmer)
* leftspeed: the speed when the left rollershutter controller is pressed or command "DOWN" was send
* rightspeed: the speed when the right rollershutter controller is pressed or command "UP" was send
* acceleration: acceleration overrides value from openhab.cfg
* drivemode: drivemode  overrides value from openhab.cfg

### Brick Servo
#### Whats new?
Support for Dimmer, Rollershuter and Number items. Besides that the speed
can be set using a percent value.

Number items will show the current position.

#### New item configuration options
* velocity: the velocity used to reach the new position
* max: the maximum position (Dimmer, Rollershutter)
* min: the minimum position (Dimmer, Rollershutter)
* step: the step value for increasing decreasing position (Dimmer)
* leftposition: the target position to reach when the left rollershutter controller is pressed or command "DOWN" was send
* rightposition: the target position to reach when the right rollershutter controller is pressed or command "UP" was send
* acceleration: the acceleration

### TinkerForge Action
The new openHAB action [[TinkerForgeAction|Actions]] comes up with the actions tfServoSetposition, tfClearLCD and tfDCMotorSetspeed.
tfServoSetposition(uid, num, position, velocity, acceleration) can be used to control the servo.
tfClearLCD(uid) uid is the uid of the LCD display. A call of tfClearLCD will clear the LCD display.
tfDCMotorSetspeed(String uid, String speed, String acceleration, String drivemode) can be used to control a DC motor.

  Example:

  ```
  tfServoSetposition("6Crt5W", "servo0", "-9000", "65535", "65535")
  ```

## General Remarks
The binding supports the connection to several brickd instances.

The TinkerForge auto reconnect feature is supported. Furthermore even if the initial connect failed the binding will make retries to get connected to the brickd.

## RED Brick
openHAB is preinstalled on the RED Brick image and can be configured with the TinkerForge BrickViewer.

## Generic Item Binding Configuration

### Basic Configuration

In order to connect openHAB to TinkerForge devices you need to define all the brickd hosts and ports
in the openhab.cfg file.

The following properties must be configured to define a brickd connection:

    tinkerforge:hosts="<IP address>[:port] ..."

The properties indicated by '<...>' need to be replaced with an actual value. Properties surrounded
by square brackets are optional.

| <b>Property</b> | <b>Description</b> |
| --------------- | ------------------ |
| IP address | IP address of the brickd |
| port | The listening port of of the brickd (optional, default 4223) |

For connecting several brickds, use multiple &lt;IP address&gt; statements delimited by a space.

Devices which do not support callbacks will be polled with a configurable interval, the default
 is 60000 milliseconds. This value can be changed in openhab.cfg:

    tinkerforge.refresh=<value in milliseconds>


### Example Configurations

Example for a connection to a single brickd:

    tinkerforge:hosts=127.0.0.1

Example for several brickd connections using different ports:

    tinkerforge:hosts=127.0.0.1:4224 192.168.1.50 192.168.1.104

## Advanced Configuration

There are several configuration parameters to control the behavior of the devices. The available
parameters depend on the device type.

### Overview
For most of the devices **no configuration** is needed in openhab.cfg, they can be used with reasonable
defaults. The only exception is the IO16 Bricklet (see below).

If you want to get rid of _uid_ and _subid_ statements in the items or rule file, you can use openhab.cfg
to get a _symbolic name_.

A configuration line for a TinkerForge Device looks like this in openhab.cfg:

    tinkerforge:<symbolic name>.<property>=<value>

The *symbolic name* string can be used in the items configuration as an alternative for the uid and subid values.

The following table lists the general available properties.

|<b>Property</b>|<b>Description</b>|<b>Device</b>|
|---------------|------------------|-------------|
|uid|TinkerForge uid of the device (use the Brick Viewer to get this value)|mandatory for all devices|
|type|the device type|mandatory for all devices|
|subid|  subid of the device, subid's are used if a brick/bricklet houses more then one device (e.g. the Dual Relay Bricklet)|mandatory for sub devices|

The following table shows the TinkerForge device, its device type, subid and if it is an actuator.

|<b>device</b>|<b>type name</b>|<b>subid(s)</b>|<b>Callback</b>|<b>actuator</b>|
|-------------|----------------|---------------|---------------|---------------|
|Servo Brick sub devices|servo|servo[0-6]||x|
|DC Brick|brick_dc|||x|
|Dual Relay Bricklet sub devices|dual_relay|relay[1-2]||x|
|Humidity Bricklet|bricklet_humidity||x||
|Distance IR Bricklet|bricklet_distance_ir||x||
|Temperature Bricklet|bricklet_temperature||x||
|Barometer Bricklet|bricklet_barometer||x||
|Barometer Bricklet temperature sensor sub device|barometer_temperature|temperature|||
|Ambient Light Bricklet|bricklet_ambient_light||x||
|LCD20x4 Bricklet|bricklet_LCD20x4|||x|
|LCD20x4 backlight|backlight|backlight||x|
|LCD20x4 Bricklet button sub devices|lcd_button|button[0-3]|interrupt||
|Quad Relay Bricklet|industrial_quad_relay|relay[0-3]||x|
|Industrial Digital In 4 Bricklet|bricklet_industrial_digital_4in|in[0-3]|x||
|Industrial Digital Out 4 Bricklet sub devices|bricklet_industrial_digital_4out|out[0-3]||x|
|IO-16 Bricklet|bricklet_io16||||
|IO-16 Bricklet sub devices, which should be used as input ports|iosensor|in[ab][0-7]|x||
|IO-16 Bricklet sub devices, which should be used as output ports|io_actuator|out[ab][0-7]||x|
|IO-4 Bricklet|bricklet_io4||||
|IO-4 Bricklet sub devices, which should be used as input ports|io4sensor|in[0-3]|x||
|IO-4 Bricklet sub devices, which should be used as output ports|io4_actuator|out[0-3]||x|
|Multi Touch Bricklet|bricklet_multitouch||||
|Multi Touch Bricklet electrodes|electrode|electrode[0-11}|x||
|Remote Switch Bricklet|bricklet_remote_switch|configurable|||
|Remote Switch Bricklet sub devices|remote_switch_a or remote_switch_b or remote_switch_c|from configuration||x|
|Segment Display 4x7 Bricklet|||||
|Sound Intensity Bricklet|bricklet_soundintensity||||
|TemperatureIR Bricklet|bricklet_temperatureIR||||
|TemperatureIR Bricklet sub device object temperature|object_temperature|x|||
|TemperatureIR Bricklet sub device ambient temperature|ambient_temperature|x|||
|Tilt Bricklet|bricklet_tilt||||
|Voltage Current Bricklet|bricklet_voltageCurrent||||
|Motion Detector Bricklet|motion_detector||||

### Callback and Threshold

The TinkerForge CallbackListeners - if available - are used to observe the sensor values of the
devices. These listeners are configured to update sensor values at a given time period
(callbackPeriod). The default configuration sets the **callbackPeriod** to 1 second. This value can
be changed in openhab.cfg. For now this value must be changed for every single device. The values
must be given in milliseconds.

The callbackPeriod controls the amount of traffic from the TF hardware to the binding.

In addition to the Callback a **threshold value** can be configured. This threshold means that even
if the listener reports a changed value, the value is only send to the openHAB eventbus if: the
difference between the last value and the current value is bigger than the threshold value. You can
think of it as a kind of hysteresis, it dampens the oscillation of openHAB item values.

The threshold controls the amount of  traffic from the binding to the openHAB eventbus.

### Devices
#### LED Strip Bricklet

items file entry (e.g. tinkerforge.items):
 ```
Color  led1   (Colorize)  {tinkerforge="uid=jGu, leds=0|1|4-6|8-9, colorMapping=rbg"}
Color  led2   (Colorize)  {tinkerforge="uid=jGu, leds=2-3|7|10-14, colorMapping=rbg"}
```
sitemap file entry (e.g tinkerforge.sitemap):

```
Colorpicker item=led1 icon="slider"
Colorpicker item=led2 icon="slider"
```

#### Dual Relay Bricklet
An entry in openhab.cfg is only needed if you want to use a _symbolic name_ in the items file.

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_dual_relay |

relay sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | relay1, relay2 |
| type | openHAB type name | dual_relay |

openhab.cfg:

    tinkerforge:relay_coffee_machine.uid=c21
    tinkerforge:relay_coffee_machine.type=dual_relay
    tinkerforge:relay_coffee_machine.subid=relay1

    tinkerforge:relay_garage_door.uid=c21
    tinkerforge:relay_garage_door.type=dual_relay
    tinkerforge:relay_garage_door.subid=relay2

items file entry (e.g. tinkerforge.items):

    Switch DualRelay1          "DualRelay1" { tinkerforge="name=relay_coffee_machine" }
    Switch DualRelay2          "DualRelay2" { tinkerforge="uid=c21, subid=relay2" }

sitemap file entry (e.g tinkerforge.sitemap):

    Switch item=DualRelay1
    Switch item=DualRelay2

#### Humidity Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod or
if you want to use a _symbolic name_.

openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_humidity |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

    tinkerforge:humidity_balcony.uid=b2j
    tinkerforge:humidity_balcony.type=bricklet_humidity
    tinkerforge:humidity_balcony.threshold=1
    tinkerforge:humidity_balcony.callbackPeriod=10


items file entry (e.g. tinkerforge.items):

    Number Humidity                 "Humidity [%.1f %%]"  { tinkerforge="uid=b2j" }

sitemap file entry (e.g tinkerforge.sitemap):

    Text item=Humidity

#### Moisture Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod or
if you want to use a _symbolic name_.

openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_moisture |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
```
tinkerforge:moisture.uid=kve
tinkerforge:moisture.type=bricklet_moisture
tinkerforge:moisture.threshold=0
tinkerforge:moisture.callbackPeriod=5000
tinkerforge:moisture.movingAverage=90
```

items file entry (e.g. tinkerforge.items):
```
Number Moisture                 "Moisture [%.1f]"  { tinkerforge="uid=kve" }
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=Moisture
```

#### Motion Detector Bricklet
An entry in openhab.cfg is only needed if you want to use a _symbolic name_.

openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | motion_detector |

```
tinkerforge:motion.uid=m3W
tinkerforge:motion.type=motion_detector
```

items file entry (e.g. tinkerforge.items):
```
Contact motion      "motion [MAP(en.map):MOTION%s]" {tinkerforge="uid=m3W"}
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=motion
```

en.map file
```
MOTIONCLOSED=no motion
MOTIONOPEN=montion detected
```

#### Multi Touch Bricklet
An entry in openhab.cfg is only needed if you want to adjust sensitivity, recalibrate, disable
electrodes or use a _symbolic name_.

openhab.cfg
* device configuration

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_multitouch |
| sensitivity | | value between 5-201 |
| recalibrate | recalibrate the sensor | true or false |

```
tinkerforge:touch.uid=jUS
tinkerforge:touch.type=bricklet_multitouch
tinkerforge:touch.sensitivity=181
tinkerforge:touch.recalibrate=true
```

* electrode sub device configuration

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device|electrode0 electrode1 electrode2 electrode3 electrode4 electrode5 electrode6 electrode7 electrode8 electrode9 electrode10 electrode11|
| disableElectrode | disables the electrode | true or false|
```
tinkerforge:e1.uid=jUS
tinkerforge:e1.type=electrode
tinkerforge:e1.subid=electrode1
tinkerforge:e1.disableElectrode=true
```

* proximity sub device configuration

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device|proximity|
| disableElectrode | disables the proximity detection | true or false|

```
tinkerforge:prox.uid=jUS
tinkerforge:prox.type=proximity
tinkerforge:prox.subid=proximity
tinkerforge:prox.disableElectrode=true
```

items file entry (e.g. tinkerforge.items):
```
Contact electrode0      "electrode0 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode0"}
Contact electrode1      "electrode1 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode1"}
Contact electrode2      "electrode2 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode2"}
Contact electrode3      "electrode3 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode3"}
Contact electrode4      "electrode4 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode4"}
Contact electrode5      "electrode5 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode5"}
Contact electrode6      "electrode6 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode6"}
Contact electrode7      "electrode7 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode7"}
Contact electrode8      "electrode8 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode8"}
Contact electrode9      "electrode9 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode9"}
Contact electrode10     "electrode10 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode10"}
Contact electrode11     "electrode11 [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=electrode11"}
Contact proximity       "proximity [MAP(en.map):%s]" {tinkerforge="uid=jUS, subid=proximity"}
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=electrode0
Text item=electrode1
Text item=electrode2
Text item=electrode3
Text item=electrode4
Text item=electrode5
Text item=electrode6
Text item=electrode7
Text item=electrode8
Text item=electrode9
Text item=electrode10
Text item=electrode11
Text item=proximity
```

#### Remote Switch Bricklet
An entry in openhab.cfg mandatory. You have to set sub device names $subdevice for your devices in the appropriate
type$typeDevices variable as space separated list. $type depends on the device hardware type of your switching device.
You must also add configuration for the $subdevice device. The available configuration variables depend
on the device type.

openhab.cfg
* device configuration

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_remote_switch |
| typeADevices | sub device names of type A devices | choose a resonable string, e.g. "kitchen bathroom" |
| typeBDevices | sub device names of type B devices  | true or false |
| typeCDevices | sub device names of type C devices  |

```
tinkerforge:rs1.uid=jKw
tinkerforge:rs1.type=bricklet_remote_switch
tinkerforge:rs1.typeADevices=rslr1 rslr2
tinkerforge:rs1.typeBDevices=kitchen
tinkerforge:rs1.typeCDevices=floor
```

* sub device type A configuration

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | must correspond to the device(A,B,C)Devices setting| e.b. kitchen|
| houseCode | the house code of the switching device | e.g. 31 |
| receiverCode | the reciever code of the switching device | e.g. 8 |
| repeats | the number of times the code is send | e.g. 5 |

```
tinkerforge:rs_living_room.uid=jKw
tinkerforge:rs_living_room.subid=rslr1
tinkerforge:rs_living_room.type=remote_switch_a
tinkerforge:rs_living_room.houseCode=31
tinkerforge:rs_living_room.receiverCode=8
```

* sub device type B configuration

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | must correspond to the device(A,B,C)Devices setting| e.b. kitchen|
| address | the address of the switching device | e.g. 344 |
| unit | the unit value of the switching device | e.g. 9 |
| repeats | the number of times the code is send | e.g. 5 |

```
tinkerforge:rs_kitchen.uid=jKw
tinkerforge:rs_kitchen.subid=kitchen
tinkerforge:rs_kitchen.type=remote_switch_b
tinkerforge:rs_kitchen.address=344
tinkerforge:rs_kitchen.unit=9
```

* sub device type C configuration

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | must correspond to the device(A,B,C)Devices setting| e.b. kitchen|
| systemCode | the system code of the switching device | e.g. A |
| deviceCode | the device code of the switching device | e.g. 8 |
| repeats | the number of times the code is send | e.g. 5 |

```
tinkerforge:rs_floor.uid=jKw
tinkerforge:rs_floor.subid=floor
tinkerforge:rs_floor.type=remote_switch_c
tinkerforge:rs_floor.systemCode=A
tinkerforge:rs_floor.deviceCode=8
```

items file entry (e.g. tinkerforge.items):
```
Switch r0           {tinkerforge="uid=jKw, subid=rslr1"}
Switch r1           {tinkerforge="uid=jKw, subid=rslr2"}
Switch rb           {tinkerforge="uid=jKw, subid=kitchen"}
Switch rc           {tinkerforge="uid=jKw, subid=floor"}```
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Switch item=r0
Switch item=r1
Switch item=rb
Switch item=rc
```

#### Distance IR Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod or
if you want to use a _symbolic name_.

openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_distance_ir |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

    tinkerforge:distance_door.uid=6GN
    tinkerforge:distance_door.type=bricklet_distance_ir
    tinkerforge:distance_door.threshold=1
    tinkerforge:distance_door.callbackPeriod=10

items file entry (e.g. tinkerforge.items):

    Number Distance                 "Distance [%.1f mm]"  { tinkerforge="uid=6GN" }

sitemap file entry (e.g tinkerforge.sitemap):

    Text item=Distance

#### Distance US Bricklet
An entry in openhab.cfg is needed if you want to adjust threshold and / or callbackPeriod, the
movingAverage or if you want to use a _symbolic name_.

openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_distanceUS |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
| movingAverage | | default 100 |

```
tinkerforge:distanceUS.uid=mXq
tinkerforge:distanceUS.type=bricklet_distanceUS
tinkerforge:distanceUS.threshold=0
tinkerforge:distanceUS.callbackPeriod=100
tinkerforge:distanceUS.movingAverage=100
```

items file entry (e.g. tinkerforge.items):

```
Number DistanceUS                 "DistanceUS [%.1f]"  { tinkerforge="uid=mXq" }
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=DistanceUS
```

#### Temperature Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod or
if you want to use a _symbolic name_.

openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_temperature |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

    tinkerforge:barometer_balcony.uid=etd
    tinkerforge:barometer_balcony.type=bricklet_temperature
    tinkerforge:barometer_balcony.callbackPeriod=10000
    tinkerforge:barometer_balcony.threshold=1000

items file entry (e.g. tinkerforge.items):

    Number Temperature                 "Temperature [%.1f Cel]"  { tinkerforge="uid=etd" }

sitemap file entry (e.g tinkerforge.sitemap):

    Text item=Temperature

#### Barometer Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod or
if you want to use a _symbolic name_.

The temperature sub device does not support callbackPeriod, it will be polled. The polling interval
can be configured using tinkerforge:refresh property).

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_barometer |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

temperature sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | temperature |
| type | openHAB type name | barometer_temperature |

openhab.cfg:

    tinkerforge:barometer_balcony.uid=d8G
    tinkerforge:barometer_balcony.type=bricklet_barometer
    tinkerforge:barometer_balcony.callbackPeriod=10000
    tinkerforge:barometer_balcony.threshold=1000

items file entry (e.g. tinkerforge.items):

    Number Barometer                 "Air Pressure [%.1f hPa]"  { tinkerforge="uid=d8G" }

sitemap file entry (e.g tinkerforge.sitemap):

    Text item=Barometer

#### TemperatureIR Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod,
if you want to use a _symbolic name_ or adjust the emissivity of the object temperature device.

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_barometer |

object temperature sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | object_temperature |
| type | openHAB type name | object_temperature |
| emissivity |  emissivity that is used to calculate the surface temperature | a factor of 65535 e.g. 6553|
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

openhab.cfg
```
tinkerforge:objIR.uid=kr2
tinkerforge:objIR.subid=object_temperature
tinkerforge:objIR.type=object_temperature
tinkerforge:objIR.emissivity=65535
tinkerforge:objIR.threshold=0
```

ambient temperature sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | ambient_temperature |
| type | openHAB type name | ambient_temperature |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

openhab.cfg:
```
tinkerforge:ambIR.uid=kr2
tinkerforge:ambIR.subid=ambient_temperature
tinkerforge:ambIR.type=ambient_temperature
tinkerforge:ambIR.threshold=0
```
items file entry (e.g. tinkerforge.items):
```
Number AmbientTemperature                 "AmbientTemperature [%.1f C]"  { tinkerforge="uid=kr2, subid=ambient_temperature" }
Number ObjectTemperature                 "ObjectTemperature [%.1f C]"  { tinkerforge="uid=kr2, subid=object_temperature" }
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=AmbientTemperature
Text item=ObjectTemperature
```

#### Voltage / Current Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod,
if you want to use a _symbolic name_ or adjust the averaging, voltage conversion time,
current conversion time of the device.

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_voltageCurrent |
|averaging|number of averages|0-7|
|voltageConversionTime|voltage conversion time|0-7|
|currentConversionTime|current conversion time|0-7|
```
tinkerforge:voltageCurrent.uid=dN5
tinkerforge:voltageCurrent.type=bricklet_voltageCurrent
tinkerforge:voltageCurrent.averaging=3
tinkerforge:voltageCurrent.voltageConversionTime=4
tinkerforge:voltageCurrent.currentConversionTime=4
```

voltage sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | voltageCurrent_voltage |
| type | openHAB type name | voltageCurrent_voltage |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

openhab.cfg
```
tinkerforge:vc_voltage.uid=dN5
tinkerforge:vc_voltage.subid=voltageCurrent_voltage
tinkerforge:vc_voltage.type=voltageCurrent_voltage
tinkerforge:vc_voltage.threshold=20
tinkerforge:vc_voltage.callbackPeriod=100
```

current sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | voltageCurrent_current |
| type | openHAB type name | voltageCurrent_current |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

openhab.cfg
```
tinkerforge:vc_current.uid=dN5
tinkerforge:vc_current.subid=voltageCurrent_current
tinkerforge:vc_current.type=voltageCurrent_current
tinkerforge:vc_current.threshold=10
tinkerforge:vc_current.callbackPeriod=100
```

power sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | voltageCurrent_power |
| type | openHAB type name | voltageCurrent_power |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

openhab.cfg
```
tinkerforge:vc_power.uid=dN5
tinkerforge:vc_power.subid=voltageCurrent_power
tinkerforge:vc_power.type=voltageCurrent_power
tinkerforge:vc_power.threshold=10
tinkerforge:vc_power.callbackPeriod=100
```


items file entry (e.g. tinkerforge.items):
```
Number Voltage       "Voltage [%d mV]"  { tinkerforge="uid=dN5, subid=voltageCurrent_voltage" }
Number Current       "Current [%d mA]"  { tinkerforge="uid=dN5, subid=voltageCurrent_current" }
Number Power         "Power [%d mW]"  { tinkerforge="uid=dN5, subid=voltageCurrent_power" }```
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=Voltage
Text item=Current
Text item=Power
```


#### Ambient Light Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod or
if you want to use a _symbolic name_.

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_ambient_light |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

openhab.cfg:

    tinkerforge:luminance_balcony.uid=ef5
    tinkerforge:luminance_balcony.type=bricklet_ambient_light
    tinkerforge:luminance_balcony.threshold=1
    tinkerforge:luminance_balcony.callbackPeriod=10

items file entry (e.g. tinkerforge.items):

    Number AmbientLight            "Luminance [%.1f Lux]" { tinkerforge="uid=ef5" }

sitemap file entry (e.g tinkerforge.sitemap):

    Text item=AmbientLight

#### Sound Intensity Bricklet
An entry in openhab.cfg is only needed if you want to adjust threshold and / or callbackPeriod or
if you want to use a _symbolic name_.

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_soundintensity |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

openhab.cfg:
```
tinkerforge:sound.uid=iQE
tinkerforge:sound.type=bricklet_soundintensity
tinkerforge:sound.threshold=1
tinkerforge:sound.callbackPeriod=5000
items file entry (e.g. tinkerforge.items):
```
items file entry (e.g. tinkerforge.items):
```
Number SoundIntensity                 "Sound [%.1f]"  { tinkerforge="uid=iQE" }
```
sitemap file entry (e.g tinkerforge.sitemap):

```
Text item=SoundIntensity
```

#### Tilt Bricklet
You can use a contact, number or switch item.

An entry in openhab.cfg is only needed if you want to use a _symbolic name_.

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_tilt |

openhab.cfg:
```
tinkerforge:tilt.uid=j7k
tinkerforge:tilt.type=bricklet_tilt
```

items file entry (e.g. tinkerforge.items):
```
Contact tiltContact     "tilt [MAP(en.map):%s]" { tinkerforge="uid=j7k" }
Number tiltSensor       "tilt [MAP(en.map):%s]"  { tinkerforge="uid=j7k" }
Switch tiltSwitch         "tilt" { tinkerforge="uid=j7k" }
```

sitemap file entry (e.g tinkerforge.sitemap):

```
Text item=tiltContact
Text item=tiltSensor
Switch item=tiltSwitch
```

en.map file entry (optional)
```
0=closed
1=open
2=vibrating
```

#### Segment Display 4x7
The LCD20x4 is a bit special as it acts as actuator which can receive number messages. To
achieve this, you have to configure the device as Number item.

An entry in openhab.cfg is only needed if you want to use a _symbolic name_.

items file entry (e.g. tinkerforge.items):
```
Number Segment7         "Segment7" { tinkerforge="uid=kQg"}
```

rules file (e.g. tinkerforge.rules):

If you want to display the object temperature from a temperatureIr Item called ObjectTemperature this
rule would do the trick for you.

```
rule "Weatherstation Segment update ObjectTemperature"
        when
                Item ObjectTemperature received update
        then
                sendCommand(Segment7, ObjectTemperature.state))
end
```

#### LCD20x4 Bricklet

The LCD20x4 is a bit special as it acts as actuator which can receive text messages. To
achieve this, you have to configure the device as String item.


What’s the meaning of this magic string?

    sendCommand(TF_LCD, String::format("TFNUM<213>%4s"Barometer.state.format("%d")
                      ))

TFNUM is just a flag to signal the binding that some position information is passed. The first
number is the line number, starting from 0. The second and third number are interpreted as the
position in the line, starting from 0.

The above example would write the current value of the barometer bricklet to line 3 starting from
position 14, with a fixed width of 4 (this is because of %4s).

openhab.cfg:

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_LCD20x4 |

backlight sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | backlight |
| type | openHAB type name | backlight |

button sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | button0, button1, button2, button3 |
| type | openHAB type name | lcd_button |


items file entry (e.g. tinkerforge.items):

    String LCD         "LCD" { tinkerforge="uid=d4j"}
    Switch LCDBacklight        "LCDBacklight" { tinkerforge="uid=d4j, subid=backlight"}
    Switch Button0         "Button0" { tinkerforge="uid=d4j, subid=button0"}
    Switch Button1         "Button1" { tinkerforge="uid=d4j, subid=button1"}
    Switch Button2         "Button2" { tinkerforge="uid=d4j, subid=button2"}
    Switch Button3         "Button3" { tinkerforge="uid=d4j, subid=button3"}

sitemap file entry (e.g tinkerforge.sitemap):

    Switch item=LCDBacklight

rules file (e.g. tinkerforge.rules):

    import org.openhab.core.library.types.*

    var Number initialSleepTime = 10

    rule "Weatherstation LCD init from Backlight"
    when
        Item TF_LCDBacklight changed from UNDEF or
            System started
    then
        createTimer(now.plusSeconds(initialSleepTime)) [|
            sendCommand(TF_LCD, "TFNUM<00>Temperature:       C")
            sendCommand(TF_LCD, "TFNUM<10>Humidity   :       %")
            sendCommand(TF_LCD, "TFNUM<20>Pressure   :     hPa")
            sendCommand(TF_LCD, "TFNUM<30>Luminance  :     Lux")
            sendCommand(TF_LCDBacklight, ON)
            sendCommand(TF_LCD, String::format("TFNUM<013>%4s",
                                    TF_Barometer_Temperature.state.format("%.1f")
                            ))
            sendCommand(TF_LCD, String::format("TFNUM<113>%4s",
                                    TF_Humdity.state.format("%.1f")
                                ))
            sendCommand(TF_LCD, String::format("TFNUM<213>%4s",
                                      TF_Barometer.state.format("%.0f")
                                      ))
            sendCommand(TF_LCD, String::format("TFNUM<313>%4s",
                                    TF_AmbientLight.state.format("%.0f")
                                    ))
        ]

    end

    rule Goodbye
    when
            System shuts down
    then
            sendCommand(TF_LCDBacklight, OFF)
    end

    rule "Weatherstation LCD Backlight"
            when
                    Item TF_Button0 received update
            then
            if (TF_Button0.state == ON)
                sendCommand(TF_LCDBacklight, ON)
            else
                sendCommand(TF_LCDBacklight, OFF)

    end

    rule "Weatherstation LCD update temperature"
            when
                    Item TF_Barometer_Temperature received update
            then
                    sendCommand(TF_LCD, String::format("TFNUM<013>%4s",
                                    TF_Barometer_Temperature.state.format("%.1f")
                            ))
    end

    rule "Weatherstation LCD update humidity"
            when
                    Item TF_Humdity received update
            then
                    sendCommand(TF_LCD, String::format("TFNUM<113>%4s",
                                    TF_Humdity.state.format("%.1f")
                                ))
    end
    rule "Weatherstation LCD update airpressure"
            when
                    Item TF_Barometer received update
            then
                    sendCommand(TF_LCD, String::format("TFNUM<213>%4s",
                                      TF_Barometer.state.format("%.0f")
                                      ))
    end
    rule "Weatherstation LCD update ambientLight"
            when
                    Item TF_AmbientLight received update
            then
                    sendCommand(TF_LCD, String::format("TFNUM<313>%4s",
                                    TF_AmbientLight.state.format("%.0f")
                                    ))
    end

#### Industrial Quad Relay Bricklet

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_industrial_quad_relay |

relay sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | relay0, relay2, relay3, relay4 |
| type | openHAB type name | industrial_quad_relay |

items file entry (e.g. tinkerforge.items):

    Switch QR1                      "QR1" {tinkerforge="uid=eQj, subid=relay0"}
    Switch QR2                      "QR2" {tinkerforge="uid=eQj, subid=relay1"}
    Switch QR3                      "QR3" {tinkerforge="uid=eQj, subid=relay2"}
    Switch QR4                      "QR4" {tinkerforge="uid=eQj, subid=relay3"}

sitemap file entry (e.g tinkerforge.sitemap):

    Switch item=QR1
    Switch item=QR2
    Switch item=QR3
    Switch item=QR4

#### Industrial Digital In 4 Bricklet

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | in0, in2, in3, in4 |
| type | openHAB type name | bricklet_industrial_digital_4in |
| debouncePeriod | debounce time in ms | default=100 |

input port sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | in0, in2, in3, in4 |
| type | openHAB type name |  |

openhab.cfg:

    tinkerforge:inddi4.uid=efY
    tinkerforge:inddi4.type=bricklet_industrial_digital_4in
    tinkerforge:inddi4.debouncePeriod=100

items file entry (e.g. tinkerforge.items):

    Contact ID1                     "ID1 [MAP(en.map):%s]" {tinkerforge="uid=efY, subid=in0"}
    Contact ID2                     "ID2 [MAP(en.map):%s]" {tinkerforge="uid=efY, subid=in1"}
    Contact ID3                     "ID3 [MAP(en.map):%s]" {tinkerforge="uid=efY, subid=in2"}
    Contact ID4                     "ID4 [MAP(en.map):%s]" {tinkerforge="uid=efY, subid=in3"}

sitemap file entry (e.g tinkerforge.sitemap):

    Text item=ID1
    Text item=ID2
    Text item=ID3
    Text item=ID4

#### Industrial Digital Out 4 Bricklet

output port sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | out0, out2, out3, out4 |
| type | openHAB type name |  |

openhab.cfg: no configuration needed

items file entry (e.g. tinkerforge.items):

```
Switch di4out0      {tinkerforge="uid=fG6, subid=out0"}
Switch di4out1      {tinkerforge="uid=fG6, subid=out1"}
Switch di4out2      {tinkerforge="uid=fG6, subid=out2"}
Switch di4out3      {tinkerforge="uid=fG6, subid=out3"}
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Switch item=di4out0
Switch item=di4out1
Switch item=di4out2
Switch item=di4out3
```

#### IO-16 Bricklet

openhab.cfg:

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_io16 |
| debouncePeriod | debounce time in ms | default=100 |

iosensor sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | ina0, ina1, ina2, ina3, ina4, ina5, ina6, ina7, inb0, inb1, inb2, inb3, inb4, inb5, inb6, inb7 |
| type | openHAB type name | iosensor |
| pullUpResistorEnabled | enable the pull-up resistor |  true, false |

io_actor sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | outa0, outa1, outa2, outa3, outa4, outa5, outa6, outa7, outb1, outb2, outb3, outb4, outb5, outb6, outb7 |
| type | openHAB type name | io_actuator |
| defaultState | default state of the port, true = HIGH, false=LOW | true, false |

    tinkerforge:io16.uid=efY
    tinkerforge:io16.type=bricklet_io16
    tinkerforge:io16.debouncePeriod=100

    tinkerforge:io16ina0.uid=efY
    tinkerforge:io16ina0.subid=ina0
    tinkerforge:io16ina0.type=iosensor
    tinkerforge:io16ina0.pullUpResistorEnabled=true

    tinkerforge:io16ina1.uid=efY
    tinkerforge:io16ina1.subid=ina1
    tinkerforge:io16ina1.type=iosensor
    tinkerforge:io16ina1.pullUpResistorEnabled=true

    tinkerforge:io16outa2.uid=efY
    tinkerforge:io16outa2.subid=outa2
    tinkerforge:io16outa2.type=io_actuator
    tinkerforge:io16outa2.defaultState=true

items file entry (e.g. tinkerforge.items):

    Contact ina0            "ina0 [MAP(en.map):%s]" {tinkerforge="uid=efY, subid=ina0"}
    Contact ina1            "ina0 [MAP(en.map):%s]" {tinkerforge="uid=efY, subid=ina1"}
    Switch outa2            "outb0" {tinkerforge="uid=efY, subid=outa2"}

sitemap file entry (e.g tinkerforge.sitemap):

    Text item=ina0
    Text item=ina01
    Switch item=outa2

#### IO-4 Bricklet

openhab.cfg:

bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_io4 |
| debouncePeriod | debounce time in ms | default=100 |

io4sensor sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | in0, in1, in2, in3|
| type | openHAB type name | io4sensor |
| pullUpResistorEnabled | enable the pull-up resistor |  true, false |

io4_actor sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | out0, out1, out2, out3|
| type | openHAB type name | io4_actuator |
| defaultState | default state of the port, true = HIGH, false=LOW | true, false |

```
tinkerforge:io4in0.uid=h56
tinkerforge:io4in0.subid=in0
tinkerforge:io4in0.type=io4sensor
tinkerforge:io4in0.pullUpResistorEnabled=true

tinkerforge:io4in1.uid=h56
tinkerforge:io4in1.subid=in1
tinkerforge:io4in1.type=io4sensor
tinkerforge:io4in1.pullUpResistorEnabled=true

tinkerforge:io4out2.uid=h56
tinkerforge:io4out2.subid=out2
tinkerforge:io4out2.type=io4_actuator
tinkerforge:io4out2.defaultState=keep
#tinkerforge:io4out2.keepOnReconnect=true

tinkerforge:io4out3.uid=h56
tinkerforge:io4out3.subid=out3
tinkerforge:io4out3.type=io4_actuator
tinkerforge:io4out3.defaultState=true
tinkerforge:io4out3.keepOnReconnect=false

#tinkerforge:io4in2.uid=h56
#tinkerforge:io4in2.subid=in2
#tinkerforge:io4in2.type=io4sensor
#tinkerforge:io4in2.pullUpResistorEnabled=true

#tinkerforge:io4in3.uid=h56
#tinkerforge:io4in3.subid=in3
#tinkerforge:io4in3.type=io4sensor
#tinkerforge:io4in3.pullUpResistorEnabled=true
```

items file entry (e.g. tinkerforge.items):
```
Contact in0     "in0 [MAP(en.map):%s]" {tinkerforge="uid=h56, subid=in0"}
Contact in1     "in1 [MAP(en.map):%s]" {tinkerforge="uid=h56, subid=in1"}
Switch  out2     "out2" {tinkerforge="uid=h56, subid=out2"}
Switch  out3     "out3" {tinkerforge="uid=h56, subid=out3"}
```

sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=in0
Text item=in1
Switch item=out2
Switch item=out3
```

#### DC Brick

For the DC Brick you can configure the acceleration, the pwm frequency, the drive mode (break=0, coast=1)
and the switchOnVelocity. The device type is brick_dc. Valid values for driveMode are Break and Coast.

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | brick_dc |
| acceleration | | default=10000 |
| pwmFrequency | | default=15000 |
| driveMode | | default=0, values: 0=Break, 1=Coast |
| switchOnVelocity | | default=10000 |


openhab.cfg:

    tinkerforge:dc_garage.uid=62Zduj
    tinkerforge:dc_garage.type=brick_dc
    tinkerforge:dc_garage.switchOnVelocity=10000
    tinkerforge:dc_garage.pwmFrequency=15000
    tinkerforge:dc_garage.driveMode=0
    tinkerforge:dc_garage.acceleration=10000

items file entry (e.g. tinkerforge.items):

    Switch TinkerforgeDC          "TinkerforgeDC" { tinkerforge="uid=62Zduj"}

sitemap file entry (e.g tinkerforge.sitemap):

    Switch item=TinkerforgeDC

#### Servo Brick

For the Servo Brick you can configure the velocity, acceleration, servo voltage, pulse width min,
pulse width max, period and the output voltage (must be equal for all servos). The device type is
"servo". Available subid's are servo0 to servo6.

The current implementation is more or less for demo purposes. The servo can only be used as a switch
item, to move the servo to the most left or most right position.

brick:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | brick_servo |

servo sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as brick |
| subid | openHAB subid of the device | servo0, servo1, servo2, servo3, servo4, servo5, servo6 |
| type | openHAB type name | servo |
| velocity | | default=30000 |
| acceleration | | default=30000 |
| servoVoltage | | |
| pulseWidthMin | | default=1000 |
| pulseWidthMax | | default=2000 |
| period | | default=19500 |
| outputVoltage | output voltage can only be set once (will be used for all servos) | default=5000 |

openhab.cfg:

    tinkerforge:servo0.uid=6Crt5W
    tinkerforge:servo0.type=servo
    tinkerforge:servo0.subid=servo0
    tinkerforge:servo0.velocity=65530
    tinkerforge:servo0.acceleration=65530

items file entry (e.g. tinkerforge.items):

    Switch Servo0              "Servo0" { tinkerforge="uid=6Crt5W, subid=servo0" }
    Switch Servo1              "Servo1" { tinkerforge="uid=6Crt5W, subid=servo1" }
    Switch Servo2              "Servo2" { tinkerforge="uid=6Crt5W, subid=servo2" }
    Switch Servo3              "Servo3" { tinkerforge="uid=6Crt5W, subid=servo3" }
    Switch Servo4              "Servo4" { tinkerforge="uid=6Crt5W, subid=servo4" }
    Switch Servo5              "Servo5" { tinkerforge="uid=6Crt5W, subid=servo5" }
    Switch Servo6              "Servo6" { tinkerforge="uid=6Crt5W, subid=servo6" }

sitemap file entry (e.g tinkerforge.sitemap):

    Switch item=Servo0
    Switch item=Servo1
    Switch item=Servo2
    Switch item=Servo3
    Switch item=Servo4
    Switch item=Servo5
    Switch item=Servo6

## Item Binding Configuration

In order to bind an item to a device, you need to provide configuration settings. The easiest way
to do so is to add binding information in your item file (in the folder '${openhab_home}/configurations/items').

The configuration of the TinkerForge binding item looks like this:

    tinkerforge="(uid=<id> [, subid=<id>] | name=<name>)"

The configuration is quite simple. You either have to set a value for the uid and optionally for the
subid of the device, or - if the device is configured in openhab.cfg - the "symbolic name" of the device.

| Property | Description |
| -------- | ----------- |
| uid      | TinkerForge uid of the device (Use the Brick Viewer to get this value) |
| subid    | optional subid of the device|
| name     | _symbolic name_ of the device. The name is only available if there is some configuration for the device in openhab.cfg. |

## Item Types
Supported item types are "Switch Item", "Number Item", "Contact Item" and "String Item".
