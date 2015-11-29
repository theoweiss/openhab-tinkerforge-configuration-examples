(Work in progress ...)
======================

Documentation of the TinkerForge binding bundle

## Table of Contents
- [Introduction](#introduction)
- [Generic Item Binding Configuration](#generic-item-binding-configuration)
  - [Basic Configuration](#basic-configuration)
    - [Item Binding Configuration](#item-binding-configuration)
    - [Item Types](#item-types)
    - [Example Configuration](#example-configuration)
- [Advanced Configuration](#advanced-configuration)
    - [Overwiew](#overview)
    - [Callback and threshold](#callback-and-threshold)
    - [Supported Devices](#supported-devices)
      - Bricks
        - [DC Brick](#dc-brick)
        - [Servo Brick](#servo-brick)
      - Bricklets
        - [Ambient Light Bricklet](#ambient-light-bricklet-v2)
        - [Barometer Bricklet, barometer and temperature device](#barometer-bricklet)
        - [Distance IR Bricklet](#distance-ir-bricklet)
        - [Distance US Bricklet](#distance-us-bricklet)
        - [Dual Button Bricklet](#dual-button-bricklet)
        - [Dual Relay Bricklet](#dual-relay-bricklet)
        - [Dust Detector Bricklet](#dust-detector-bricklet)
        - [Hall Effect Bricklet](#hall-effect-bricklet)
        - [Humidity Bricklet](#humidity-bricklet)
        - [Industrial Digital In 4 Bricklet](#industrial-digital-in-4-bricklet)
        - [Industrial Digital Out 4 Bricklet](#industrial-digital-out-4-bricklet)
        - [Industrial Dual 0-20mA Bricklet](#industrial-dual-0-20ma-bricklet)
        - [Industrial Quad Relay Bricklet](#industrial-quad-relay-bricklet)
        - [IO 16 Bricklet](#io-16-bricklet)
        - [Joystick Bricklet](#joystick-bricklet)
        - [LCD 20×4 Display Bricklet](#lcd-20x4-display-bricklet)
        - [LED Strip Bricklet](#led-strip-bricklet)
        - [Linear Poti Bricklet](#linear-poti-bricklet)
        - [Load Cell Bricklet](#load-cell-bricklet)
        - [Motion Detector Bricklet](#motion-detector-bricklet)
        - [Multi Touch Bricklet](#multi-touch-bricklet)
        - [Moisture Bricklet](#moisture-bricklet)
        - [Pieco Speaker Bricklet](#piezo-speaker-bricklet)
        - [PTC Bricklet](#ptc-bricklet)
        - [Remote Switch Bricklet](#remote-switch-bricklet)
        - [Rotary Encoder Bricklet](#rotary-encoder-bricklet)
        - [Segment Display 4x7 Bricklet](#segment-display-4x7-bricklet)
        - [Solid State Relay Bricklet](#solid-state-relay-bricklet)
        - [Sound Intensity Bricklet](#sound-intensity-bricklet)
        - [Temperature Bricklet](#temperature-bricklet)
        - [Temperature IR Bricklet](#temperature-ir-bricklet)
        - [Tilt Bricklet](#tilt-bricklet)
        - [Voltage/Current Bricklet](#voltagecurrent-bricklet)
      - Kits
        - [Weatherstation Kit](#weatherstation-kit)
      - Deamon
        - [Brick Daemon](#brick-daemon)
- [News](#news)
- [Red Brick](#red-brick)
- [Tinkerforge Actions](#tinkerforge-actions)
- [Upgrading](#upgrading)
  - [from Version 1.3](#upgrading-from-13)
  - [from Version 1.4](#upgrading-from-14)
  - [from Version 1.7](#upgrading-from-17)
  - [from Version 1.8](#upgrading-from-18)

---

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

The binding supports the connection to several brickd instances.
The TinkerForge auto reconnect feature is supported. Furthermore even if the initial connect failed the binding will make retries to get connected to the brickd.

The TinkerForge binding bundle is available as a separate (optional) download. For installation of
the binding, please see Wiki page [[Bindings]].

---

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

#### Item Binding Configuration

In order to bind an item to a device, you need to provide configuration settings. The easiest way
to do so is to add binding information in your item file (in the folder '${openhab_home}/configurations/items').

The configuration of the TinkerForge binding item looks like this:

    tinkerforge="(uid=<your_id> [, subid=<your_subid>] | name=<your_name>)"

The configuration is quite simple. You either have to set a value for the uid and optionally for the
subid of the device, or - if the device is configured in openhab.cfg - the "symbolic name" of the device.

| Property | Description |
| -------- | ----------- |
| uid      | TinkerForge uid of the device (Use the Brick Viewer to get this value) |
| subid    | optional subid of the device|
| name     | _symbolic name_ of the device. The name is only available if there is some configuration for the device in openhab.cfg. |

#### Item Types
Supported item types are "Switch Item", "Number Item", "Contact Item" and "String Item".

#### Example Configuration

Example for a connection to a single brickd:

    tinkerforge:hosts=127.0.0.1

Example for several brickd connections using different ports:

    tinkerforge:hosts=127.0.0.1:4224 192.168.1.50 192.168.1.104

---

### Advanced Configuration
There are several configuration parameters to control the behavior of the devices. The available
parameters depend on the device type.

#### Overview
For most of the devices **no configuration** is needed in openhab.cfg, they can be used with reasonable
defaults. The only exception is the IO16 Bricklet (see below).

<a name="sym_name"></a>
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

|<b>device</b>|<b>type name</b>|<b>subid(s)</b>|<b>Callback</b>|
|-------------|----------------|---------------|---------------|
|Servo Brick sub devices|servo|servo[0-6]||
|DC Brick|brick_dc|||
|Dual Relay Bricklet sub devices|dual_relay|relay[1-2]||
|Humidity Bricklet|bricklet_humidity||x|
|Distance IR Bricklet|bricklet_distance_ir||x|
|Temperature Bricklet|bricklet_temperature||x|
|Barometer Bricklet|bricklet_barometer||x|
|Barometer Bricklet temperature sensor sub device|barometer_temperature|temperature||
|Ambient Light Bricklet|bricklet_ambient_light||x|
|LCD20x4 Bricklet|bricklet_LCD20x4|||
|LCD20x4 backlight|backlight|backlight||
|LCD20x4 Bricklet button sub devices|lcd_button|button[0-3]|interrupt|
|Quad Relay Bricklet|industrial_quad_relay|relay[0-3]||
|Industrial Digital In 4 Bricklet|bricklet_industrial_digital_4in|in[0-3]|x|
|Industrial Digital Out 4 Bricklet sub devices|bricklet_industrial_digital_4out|out[0-3]||
|IO-16 Bricklet|bricklet_io16|||
|IO-16 Bricklet sub devices, which should be used as input ports|iosensor|in[ab][0-7]|x|
|IO-16 Bricklet sub devices, which should be used as output ports|io_actuator|out[ab][0-7]||
|IO-4 Bricklet|bricklet_io4|||
|IO-4 Bricklet sub devices, which should be used as input ports|io4sensor|in[0-3]|x|
|IO-4 Bricklet sub devices, which should be used as output ports|io4_actuator|out[0-3]||
|Multi Touch Bricklet|bricklet_multitouch|||
|Multi Touch Bricklet electrodes|electrode|electrode[0-11}|x|
|Remote Switch Bricklet|bricklet_remote_switch|configurable||
|Remote Switch Bricklet sub devices|remote_switch_a or remote_switch_b or remote_switch_c|from configuration||
|Segment Display 4x7 Bricklet||||
|Sound Intensity Bricklet|bricklet_soundintensity|||
|TemperatureIR Bricklet|bricklet_temperatureIR|||
|TemperatureIR Bricklet sub device object temperature|object_temperature|x||
|TemperatureIR Bricklet sub device ambient temperature|ambient_temperature|x||
|Tilt Bricklet|bricklet_tilt|||
|Voltage Current Bricklet|bricklet_voltageCurrent|||
|Motion Detector Bricklet|motion_detector||||

---

<a name="call_thresh"></a>
#### Callback and Threshold

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

---

## Supported Devices

### DC Brick

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricks/DC_Brick.html#dc-brick)

#### Binding properties:

~~https://bintray.com/artifact/download/theoweiss/generic/org.openhab.binding.tinkerforge-1.7.0-SNAPSHOT.jar
The binding will work with 1.6.x versions of openHAB.~~

##### Incompatible changes
* DriveMode now is one of "brake" or "coast" instead of "0" or "1"
```
tinkerforge:dc_garage.driveMode=brake
```

* switchOnVelocity in openhab.cfg is no longer needed and has gone.
It is replaced by per item configuration:
With the benefit that you can have serveral switch items with different speeds.
~~tinkerforge:dc_garage.switchOnVelocity=10000~~
```
Switch DCSWITCH "DC Switch" {tinkerforge="uid=<your_uid>, speed=14000"}
```

##### Whats new?
Support for Dimmer, Rollershuter and Number items. Besides that the speed
can be set using a percent value.

The number items show the current velocity. The values are reported using the VelocityListener.
"callbackPeriod" and "threshold" for the listener can be configured in openhab.cfg. There is more
documentation about callback listeners at the official openHAB TinkerForgeBindig wiki page.

* callbackPeriod: milliseconds
* threshold: numeric value

##### New item configuration options
* speed: the target speed (Switch)
* max: the maximum speed (Dimmer, Rollershutter)
* min: the minimum speed (Dimmer, Rollershutter)
* step: the step value for increasing decreasing speed (Dimmer)
* leftspeed: the speed when the left rollershutter controller is pressed or command "DOWN" was send
* rightspeed: the speed when the right rollershutter controller is pressed or command "UP" was send
* acceleration: acceleration overrides value from openhab.cfg
* drivemode: drivemode  overrides value from openhab.cfg

#### Example

##### openhab.cfg:

```
tinkerforge:dc_garage.uid=<your_uid>
tinkerforge:dc_garage.type=brick_dc
tinkerforge:dc_garage.pwmFrequency=15000
tinkerforge:dc_garage.driveMode=break
tinkerforge:dc_garage.acceleration=10000
tinkerforge:dc_garage.callbackPeriod=100
```
##### Items file entry (e.g. tinkerforge.items):
```
Dimmer  DCDIMMER  "Dimmer" {tinkerforge="uid=<your_uid>, max=20000, min=-15000, acceleration=10000, drivemode=brake, step=2500"}
Dimmer  DIMMERPERCENT  "Dimmerpercent" {tinkerforge="uid=<your_uid>, max=20000, min=0, acceleration=10000, drivemode=brake, step=2500"}
Rollershutter TF_ROLLER "Roller" {tinkerforge="uid=<your_uid>, leftspeed=10000, rightspeed=-10000, acceleration=10000, drivemode=brake"}
Switch DCSWITCH "DC Switch" {tinkerforge="uid=<your_uid>, speed=14000"}
Number DCSPEED "DC Speed [%.0f]"  {tinkerforge="uid=<your_uid>, max=20000, min=-15000, step=1000, leftspeed=10000, rightspeed=-10000, acceleration=10000, drivemode=brake"}
Dimmer  RULEDIMMER  "RuleDimmer"
Switch  DCMOVE  "Action Move"
```
##### Sitemap file entry (e.g tinkerforge.sitemap):

```
sitemap tf_weather label="Brick DC"
{
  Frame {
        Switch item=TF_ROLLER
        Switch item=DCSWITCH
        Slider item=DCDIMMER
        Text item=DCSPEED
        Setpoint item=DCSPEED minValue=-1500 maxValue=25000 step=1000
        Slider item=RULEDIMMER
        }
}
```
##### Rules file (e.g. tinkerforge.rules):
```
import org.openhab.core.library.types.*

rule "percentdimmer"
    when
        Item RULEDIMMER received command INCREASE
    then
       sendCommand(DIMMERPERCENT, new PercentType(100))
end

rule "percentdimmerdecrease"
    when
        Item RULEDIMMER received command DECREASE
    then
       sendCommand(DIMMERPERCENT, new PercentType(25))
end


rule "move motor"
    when
        Item DCMOVE received command ON
    then
       var Integer acceleration = 10000
       var Short speed = 15000
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
end

```

---

### Servo Brick

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricks/Servo_Brick.html#servo-brick)

#### Binding properties:

##### Whats new?
Support for Dimmer, Rollershuter and Number items. Besides that the speed
can be set using a percent value.

Number items will show the current position.

##### New item configuration options
* velocity: the velocity used to reach the new position
* max: the maximum position (Dimmer, Rollershutter)
* min: the minimum position (Dimmer, Rollershutter)
* step: the step value for increasing decreasing position (Dimmer)
* leftposition: the target position to reach when the left rollershutter controller is pressed or command "DOWN" was send
* rightposition: the target position to reach when the right rollershutter controller is pressed or command "UP" was send
* acceleration: the acceleration

###### TinkerForge Action
The new openHAB action TinkerForgeAction comes up with the action tfServoSetposition.
tfServoSetposition(uid, num, position, velocity, acceleration) can be used to control the servo.

####### Example
```
tfServoSetposition("6Crt5W", "servo0", "-9000", "65535", "65535")
```

##### Brick:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | brick_servo |

##### Servo sub devices:

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

##### openhab.cfg:
```
tinkerforge:servo0.uid=<your_uid>
tinkerforge:servo0.type=servo
tinkerforge:servo0.subid=servo0
tinkerforge:servo0.velocity=65530
tinkerforge:servo0.acceleration=65530

tinkerforge:servo0.uid=<your_uid>
tinkerforge:servo1.type=servo
tinkerforge:servo1.subid=servo1
tinkerforge:servo1.velocity=65530
tinkerforge:servo1.acceleration=65530

tinkerforge:servo0.uid=<your_uid>
tinkerforge:servo2.type=servo
tinkerforge:servo2.subid=servo2
tinkerforge:servo2.velocity=65530
tinkerforge:servo2.acceleration=65530

tinkerforge:servo0.uid=<your_uid>
tinkerforge:servo3.type=servo
tinkerforge:servo3.subid=servo3
tinkerforge:servo3.velocity=65530
tinkerforge:servo3.acceleration=65530

tinkerforge:servo0.uid=<your_uid>
tinkerforge:servo4.type=servo
tinkerforge:servo4.subid=servo4
tinkerforge:servo4.velocity=65530
tinkerforge:servo4.acceleration=65530

tinkerforge:servo0.uid=<your_uid>
tinkerforge:servo5.type=servo
tinkerforge:servo5.subid=servo5
tinkerforge:servo5.velocity=65530
tinkerforge:servo5.acceleration=65530

tinkerforge:servo0.uid=<your_uid>
tinkerforge:servo6.type=servo
tinkerforge:servo6.subid=servo6
tinkerforge:servo6.velocity=65530
tinkerforge:servo6.acceleration=65530

```
##### Items file entry (e.g. tinkerforge.items):
```
Dimmer Servo0              "Servo0" { tinkerforge="uid=<your_uid>, subid=servo0, max=9000, min=-9000, step=500, acceleration=3000, velocity=3000" }
Dimmer Servo0Percent              "Servo0Percent" { tinkerforge="uid=<your_uid>, subid=servo0, max=9000, min=-9000, step=500, acceleration=3000, velocity=3000" }
Rollershutter Servo0Rollershutter              "Servo0Rollershutter" { tinkerforge="uid=<your_uid>, subid=servo0, max=9000, min=-9000, leftposition=3000, rightposition=-3000, acceleration=3000, velocity=3000" }
Switch Servo0Switch              "Servo0Switch" { tinkerforge="uid=<your_uid>, subid=servo0, max=9000, min=-9000, position=3000, acceleration=3000, velocity=3000" }
Number Servo0Postion              "Servo0Postion [%.0f]" { tinkerforge="uid=<your_uid>, subid=servo0, max=9000, min=-9000, step=500, acceleration=3000, velocity=3000" }
Dimmer Servo0Rule "Servo0Rule"

Switch Servo1              "Servo1" { tinkerforge="uid=<your_uid>, subid=servo1" }
Switch Servo2              "Servo2" { tinkerforge="uid=<your_uid>, subid=servo2" }
Switch Servo3              "Servo3" { tinkerforge="uid=<your_uid>, subid=servo3" }
Switch Servo4              "Servo4" { tinkerforge="uid=<your_uid>, subid=servo4" }
Switch Servo5              "Servo5" { tinkerforge="uid=<your_uid>, subid=servo5" }
Switch Servo6              "Servo6" { tinkerforge="uid=<your_uid>, subid=servo6" }

Switch ClearLCD            "ClearLCD"

Switch MoveServo "MoveServo" {autoupdate="false"}
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf_weather label="Brick Servo"
{
  Frame {
	Slider item=Servo0
	Switch item=Servo0Rollershutter
	Switch item=Servo0Switch
	Text item=Servo0Postion
	Setpoint item=Servo0Postion minValue=-9000 maxValue=6000 step=2000
	Slider item=Servo0Rule

	Switch item=ClearLCD
	Switch item=MoveServo

	Switch item=Servo1
	Switch item=Servo2
	Switch item=Servo3
	Switch item=Servo4
	Switch item=Servo5
	Switch item=Servo6
	}
}

```

---

### Ambient Light Bricklet V2

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Ambient_Light_V2.html)

#### Binding properties:
An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_ambient_light |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:ambientlightv2.uid=uK9
tinkerforge:ambientlightv2.type=bricklet_ambient_lightv2
tinkerforge:ambientlightv2.illuminanceRange=10
tinkerforge:ambientlightv2.integrationTime=10
tinkerforge:ambientlightv2.callbackPeriod=10
tinkerforge:ambientlightv2.threshold=0
```
##### Items file entry (e.g. tinkerforge.items):
```
Number illuminance "Illuminance [%.2f]" {tinkerforge="uid=<your_uid>"}
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="TinkerForge AmbientLightV2"
{
    Frame label="PTC" {
        Text item=illuminance
    }
}

```

---

### Barometer Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Barometer.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

The temperature sub device does not support callbackPeriod, it will be polled. The polling interval
can be configured using tinkerforge:refresh property.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_barometer |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### Temperature sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | temperature |
| type | openHAB type name | barometer_temperature |

##### openhab.cfg:
```
tinkerforge:barometer_balcony.uid=<your_uid>
tinkerforge:barometer_balcony.type=bricklet_barometer
tinkerforge:barometer_balcony.callbackPeriod=10000
tinkerforge:barometer_balcony.threshold=1000
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Barometer "Air Pressure [%.1f hPa]"  { tinkerforge="uid=<your_uid>" }
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=Barometer
```

---

### Distance IR Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Distance_US.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_distance_ir |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
```
tinkerforge:distance_door.uid=<your_uid>
tinkerforge:distance_door.type=bricklet_distance_ir
tinkerforge:distance_door.threshold=1
tinkerforge:distance_door.callbackPeriod=10
```
##### Items file entry (e.g. tinkerforge.items):
```
Number Distance                 "Distance [%.1f mm]"  { tinkerforge="uid=<your_uid>" }
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=Distance
```

---

### Distance US Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Industrial_Quad_Relay.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_distanceUS |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
| movingAverage | | default 100 |

##### openhab.cfg:
```
tinkerforge:distanceUS.uid=<your_uid>
tinkerforge:distanceUS.type=bricklet_distanceUS
tinkerforge:distanceUS.threshold=0
tinkerforge:distanceUS.callbackPeriod=100
tinkerforge:distanceUS.movingAverage=100
```

##### Items file entry (e.g. tinkerforge.items):
```
Number DistanceUS                 "DistanceUS [%.1f]"  { tinkerforge="uid=<your_uid>" }
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=DistanceUS
```

---

### Dual Button Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Dual_Button.html)

#### Binding properties:

Dual Button Bricklet
====================
The Dual Button Bricklet has four sub devices: two leds and two buttons.
The subids are:
 * dualbutton_leftled
 * dualbutton_rightled
 * dualbutton_leftbutton
 * dualbutton_rightbutton

##### Leds
There are two operating modes for the leds: with the autotoggle=True the leds are
automatically toggled whenever the corresponding button is pressed. With the autotoggle=False
mode the leds are fully controlled with openHAB UIs or rules. The default autotoggle mode is
autotoggle=False. The autotoggle mode can be configured using openhab.cfg.

##### Buttons
There are also two operating modes for the buttons. The buttons can behave like a switch or
like a tactile switch.  
* Switch mode
The switch mode operates like this: pressing the button toggles the
switch state, if state was ON it goes to OFF and vice versa. Releasing the button doesn't
change anything, only the next button press will change the state.
* Tactile switch mode
Pressing the button changes the switch state to ON and releasing the button changes the
state back to OFF again.

##### openhab.cfg:
```
tinkerforge:led1.uid=mN2
tinkerforge:led1.subid=dualbutton_leftled
tinkerforge:led1.type=dualbutton_led
tinkerforge:led1.autotoggle=True

tinkerforge:led2.uid=mN2
tinkerforge:led2.subid=dualbutton_rightled
tinkerforge:led2.type=dualbutton_led
tinkerforge:led2.autotoggle=False

tinkerforge:button1.uid=mN2
tinkerforge:button1.subid=dualbutton_leftbutton
tinkerforge:button1.type=dualbutton_button
tinkerforge:button1.tactile=False

tinkerforge:button2.uid=mN2
tinkerforge:button2.subid=dualbutton_rightbutton
tinkerforge:button2.type=dualbutton_button
tinkerforge:button2.tactile=True
```

##### Items file entry (e.g. tinkerforge.items):
```
Contact LeftButton              "LeftButton" { tinkerforge="uid=<your_uid>, subid=dualbutton_leftbutton"}
Contact RightButton             "RightButton" { tinkerforge="uid=<your_uid>, subid=dualbutton_rightbutton"}
Switch LeftLed                  "LeftLed" { tinkerforge="uid=<your_uid>, subid=dualbutton_leftled"}
Switch RightLed                 "RightLed" { tinkerforge="uid=<your_uid>, subid=dualbutton_rightled"}
```

##### Rules (e.g. tinkerforge.rules):
```
import org.openhab.core.library.types.*

rule "toggleright"
    when
        Item RightButton changed
    then
        logDebug("dualbutton", "{}", RightButton.state)
        if (RightButton.state == OPEN)
            sendCommand(RightLed, ON)
        else
            sendCommand(RightLed, OFF)
end
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="DualButton"
{
  Frame {
    Text item=LeftButton
    Text item=RightButton
    Switch item=LeftLed
    Switch item=RightLed
    }
}
```
---

### Dual Relay Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Dual_Relay.html)

#### Binding properties:
An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

The Dual Relay Bricklet has two sub devices.
The subids are:
 * relay1
 * relay2

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_dual_relay |

##### Relay sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | relay1, relay2 |
| type | openHAB type name | dual_relay |

##### openhab.cfg:
```
tinkerforge:relay_coffee_machine.uid=c21
tinkerforge:relay_coffee_machine.type=dual_relay
tinkerforge:relay_coffee_machine.subid=relay1

tinkerforge:relay_garage_door.uid=c21
tinkerforge:relay_garage_door.type=dual_relay
tinkerforge:relay_garage_door.subid=relay2
```

##### Items file entry (e.g. tinkerforge.items):
```
Switch DualRelay1          "DualRelay1" { tinkerforge="name=relay_coffee_machine" }
Switch DualRelay2          "DualRelay2" { tinkerforge="uid=<your_uid>, subid=relay2" }
```

##### Rules (e.g. tinkerforge.rules):
```
import org.openhab.core.library.types.*

rule "toggleright"
    when
        Item RightButton changed
    then
        logDebug("dualbutton", "{}", RightButton.state)
        if (RightButton.state == OPEN)
            sendCommand(RightLed, ON)
        else
            sendCommand(RightLed, OFF)
end
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="DualRelay"
{
  Frame {
        Switch item=DualRelay1
        Switch item=DualRelay2
    }
}

```
---

### Dust Detector Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Dust_Detector.html)

#### Binding properties:

##### openhab.cfg:
```
tinkerforge:dust.uid=uBH
tinkerforge:dust.type=bricklet_dustdetector
tinkerforge:dust.callbackPeriod=10
tinkerforge:dust.threshold=0
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Dust              "Dust [%.0f]" { tinkerforge="uid=<your_uid>"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="Dust Detector"
{
  Frame {
	Text item=Dust
	}
}

```
---

### Hall Effect Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Hall_Effect.html)

#### Binding properties:

##### openhab.cfg:
```
# really optional settings
tinkerforge:hall.uid=kp2
tinkerforge:hall.type=bricklet_halleffect
tinkerforge:hall.callbackPeriod=1000

```

##### Items file entry (e.g. tinkerforge.items):
```
Contact HallEffect       "Hall" { tinkerforge="uid=<your_uid>"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="TinkerForge Halleffect"
{
    Frame label="HallEffect" {
        Text item=HallEffect
    }
}

```
---

### Humidity Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Humidity.html)

#### Binding properties:
An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_humidity |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
```
tinkerforge:humidity_balcony.uid=<your_uid>
tinkerforge:humidity_balcony.type=bricklet_humidity
tinkerforge:humidity_balcony.threshold=1
tinkerforge:humidity_balcony.callbackPeriod=10
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Humidity "Humidity [%.1f %%]"  { tinkerforge="uid=<your_uid>" }
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=Humidity
```

---

### Industrial Digital IN 4 Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Industrial_Quad_Relay.html)

#### Binding properties:

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | in0, in2, in3, in4 |
| type | openHAB type name | bricklet_industrial_digital_4in |
| debouncePeriod | debounce time in ms | default=100 |

##### Input port sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | in0, in2, in3, in4 |
| type | openHAB type name |  |

##### openhab.cfg:
```
tinkerforge:inddi4.uid=<your_uid>
tinkerforge:inddi4.type=bricklet_industrial_digital_4in
tinkerforge:inddi4.debouncePeriod=100
```
##### Items file entry (e.g. tinkerforge.items):
```
Contact ID1                     "ID1 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in0"}
Contact ID2                     "ID2 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in1"}
Contact ID3                     "ID3 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in2"}
Contact ID4                     "ID4 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in3"}
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=ID1
Text item=ID2
Text item=ID3
Text item=ID4
```
---

### Industrial Digital Out 4 Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Industrial_Digital_Out_4.html)

#### Binding properties:

##### Output port sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | out0, out2, out3, out4 |
| type | openHAB type name |  |

##### openhab.cfg:
no configuration needed

##### Items file entry (e.g. tinkerforge.items):

```
Switch di4out0      {tinkerforge="uid=<your_uid>, subid=out0"}
Switch di4out1      {tinkerforge="uid=<your_uid>, subid=out1"}
Switch di4out2      {tinkerforge="uid=<your_uid>, subid=out2"}
Switch di4out3      {tinkerforge="uid=<your_uid>, subid=out3"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Switch item=di4out0
Switch item=di4out1
Switch item=di4out2
Switch item=di4out3
```
---

### Industrial Dual 0-20mA Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Industrial_Dual_020mA.html)

#### Binding properties:

* two sub devices: sensor0 and sensor1
* device type bricklet: bricklet_industrialdual020ma
* device type sensors: industrial020ma_sensor
* sensor values are reported as milli ampere

##### openhab.cfg configuration options for the Bricklet
sampleRate: possible values 0, 1, 2, 3. Setting the sample rate is optional
it defaults to 3 (4 samples per second).
 * 0 means: 240 samples per second
 * 1 means: 60 samples per second
 * 2 means: 15 samples per second
 * 3 means: 4 samples per second

##### openhab.cfg configuration options for the sensors
callbackPeriod: Setting the callback period is optional, the default is 1000 milli seconds.
threshold:

##### openhab.cfg
```
tinkerforge:brickletid020ma.uid=s6t
tinkerforge:brickletid020ma.type=bricklet_industrialdual020ma

tinkerforge:temperature0.uid=s6t
tinkerforge:temperature0.subid=sensor0
tinkerforge:temperature0.type=industrial020ma_sensor
tinkerforge:temperature0.callbackPeriod=100

tinkerforge:temperature1.uid=s6t
tinkerforge:temperature1.subid=sensor1
tinkerforge:temperature1.type=industrial020ma_sensor
tinkerforge:temperature1.callbackPeriod=1000
```

##### Items file entry (e.g. tinkerforge.items):
```
Number temperature0 "Temperature0 [%.2f]" {tinkerforge="uid=<your_uid>, subid=sensor0"}
Number temperature1 "Temperature1 [%.2f]" {tinkerforge="uid=<your_uid>, subid=sensor1"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="TinkerForge 020"
{
    Frame label="PTC" {
        Text item=temperature0
        Text item=temperature1
    }
}
```
---

### Industrial Quad Relay Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Industrial_Quad_Relay.html)

#### Binding properties:

The Quad Relay Bricklet has four sub devices.
The subids are:
* relay0
* relay1
* relay2
* relay3

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_industrial_quad_relay|

##### Relay sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | relay0, relay2, relay3, relay4 |
| type | openHAB type name | industrial_quad_relay |

##### openhab.cfg:
```
tinkerforge:relay0.uid=eQj
tinkerforge:relay0.type=quad_relay
tinkerforge:relay0.subid=relay0

tinkerforge:relay1.uid=eQj
tinkerforge:relay1.type=quad_relay
tinkerforge:relay1.subid=relay1

tinkerforge:relay2.uid=eQj
tinkerforge:relay2.type=quad_relay
tinkerforge:relay2.subid=relay2

tinkerforge:relay3.uid=eQj
tinkerforge:relay3.type=quad_relay
tinkerforge:relay3.subid=relay3
```

##### Items file entry (e.g. tinkerforge.items):
```
Switch QR1 "QR1" {tinkerforge="uid=<your_uid>, subid=relay0"}
Switch QR2 "QR2" {tinkerforge="uid=<your_uid>, subid=relay1"}
Switch QR3 "QR3" {tinkerforge="uid=<your_uid>, subid=relay2"}
Switch QR4 "QR4" {tinkerforge="uid=<your_uid>, subid=relay3"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="QuadRelay"
{
  Frame {
            Switch item=QR1
            Switch item=QR2
            Switch item=QR3
            Switch item=QR4    
        }
}

```
---

### IO 16 Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/IO16.html)

#### Binding properties:

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_io16 |
| debouncePeriod | debounce time in ms | default=100 |

##### IO-Sensor sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | ina0, ina1, ina2, ina3, ina4, ina5, ina6, ina7, inb0, inb1, inb2, inb3, inb4, inb5, inb6, inb7 |
| type | openHAB type name | iosensor |
| pullUpResistorEnabled | enable the pull-up resistor |  true, false |

##### IO-Actor sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | outa0, outa1, outa2, outa3, outa4, outa5, outa6, outa7, outb1, outb2, outb3, outb4, outb5, outb6, outb7 |
| type | openHAB type name | io_actuator |
| defaultState | default state of the port, true = HIGH, false=LOW | true, false |

##### openhab.cfg:
```
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

tinkerforge:io16outa3.uid=efY
tinkerforge:io16outa3.subid=outa3
tinkerforge:io16outa3.type=io_actuator

tinkerforge:io16ina4.uid=efY
tinkerforge:io16ina4.subid=ina4
tinkerforge:io16ina4.type=iosensor
tinkerforge:io16ina4.pullUpResistorEnabled=true

tinkerforge:io16ina5.uid=efY
tinkerforge:io16ina5.subid=ina5
tinkerforge:io16ina5.type=iosensor
tinkerforge:io16ina5.pullUpResistorEnabled=true

tinkerforge:io16ina6.uid=efY
tinkerforge:io16ina6.subid=ina6
tinkerforge:io16ina6.type=iosensor
tinkerforge:io16ina6.pullUpResistorEnabled=true

tinkerforge:io16ina7.uid=efY
tinkerforge:io16ina7.subid=ina7
tinkerforge:io16ina7.type=iosensor
tinkerforge:io16ina7.pullUpResistorEnabled=true

#### port b

tinkerforge:io16outb0.uid=efY
tinkerforge:io16outb0.subid=outb0
tinkerforge:io16outb0.type=io_actuator

tinkerforge:io16inb1.uid=efY
tinkerforge:io16inb1.subid=inb1
tinkerforge:io16inb1.type=iosensor
tinkerforge:io16inb1.pullUpResistorEnabled=true

tinkerforge:io16inb2.uid=efY
tinkerforge:io16inb2.subid=inb2
tinkerforge:io16inb2.type=iosensor
tinkerforge:io16inb2.pullUpResistorEnabled=true

tinkerforge:io16inb3.uid=efY
tinkerforge:io16inb3.subid=inb3
tinkerforge:io16inb3.type=iosensor
tinkerforge:io16inb3.pullUpResistorEnabled=true

tinkerforge:io16inb4.uid=efY
tinkerforge:io16inb4.subid=inb4
tinkerforge:io16inb4.type=iosensor
tinkerforge:io16inb4.pullUpResistorEnabled=true

tinkerforge:io16inb5.uid=efY
tinkerforge:io16inb5.subid=inb5
tinkerforge:io16inb5.type=iosensor
tinkerforge:io16inb5.pullUpResistorEnabled=true

tinkerforge:io16inb6.uid=efY
tinkerforge:io16inb6.subid=inb6
tinkerforge:io16inb6.type=iosensor
tinkerforge:io16inb6.pullUpResistorEnabled=true

tinkerforge:io16inb7.uid=efY
tinkerforge:io16inb7.subid=inb7
tinkerforge:io16inb7.type=iosensor
tinkerforge:io16inb7.pullUpResistorEnabled=true
```

##### Items file entry (e.g. tinkerforge.items):
```
Contact ina0		"ina0 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=ina0"}
Contact ina1		"ina1 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=ina1"}
Switch outa2		"outa2" {tinkerforge="uid=<your_uid>, subid=outa2"}
Switch outa3		"outa3" {tinkerforge="uid=<your_uid>, subid=outa3"}
Contact ina4		"ina4 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=ina4"}
Contact ina5		"ina5 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=ina5"}
Contact ina6		"ina6 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=ina6"}
Contact ina7		"ina7 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=ina7"}

Switch outb0		"outa3" {tinkerforge="uid=efY, subid=outb0"}
Contact inb1		"inb1 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=inb1"}
Contact inb2		"inb2 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=inb2"}
Contact inb3		"inb3 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=inb3"}
Contact inb4		"inb4 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=inb4"}
Contact inb5		"inb5 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=inb5"}
Contact inb6		"inb6 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=inb6"}
Contact inb7		"inb7 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=inb7"}
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap io16 label="Main Menu"
{
	Frame {
		Text item=ina0
		Text item=ina1
		Switch item=outa2
		Switch item=outa3
		Text item=ina4
		Text item=ina5
		Text item=ina6
		Text item=ina7

		Switch item=outb0
		Text item=inb1
		Text item=inb2
		Text item=inb3
		Text item=inb4
		Text item=inb5
		Text item=inb6
		Text item=inb7
		Switch item=r0
		Switch item=r1
	}
}
```
---

### Joystick Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Joystick.html)

#### Binding properties:
There are three sub devices: joystick_xposition, joystick_yposition and joystick_button.
Callback period for xy position defaults to 10 milli seconds. CallbackPeriod must be configured
on bricklet_joystick device, because x and y position can not have
different callback periods.

##### Button
Two operating modes for the button. The button can behave like a switch or
like a tactile switch.  
* Switch mode
The switch mode operates like this: pressing the button toggles the
switch state, if state was ON it goes to OFF and vice versa. Releasing the button doesn't
change anything, only the next button press will change the state.
* Tactile switch mode
Pressing the button changes the switch state to ON and releasing the button changes the
state back to OFF again.

Switch Mode is the default mode you can change the mode to tactile by adding a line like this
to your openhab.cfg:
```
tinkerforge:joystickbutton.tactile=True
```

##### openhab.cfg:
```
tinkerforge:joystick.uid=aXJ
tinkerforge:joystick.type=bricklet_joystick
tinkerforge:joystick.callbackPeriod=1000

tinkerforge:joystickbutton.uid=aXJ
tinkerforge:joystickbutton.subid=joystick_button
tinkerforge:joystickbutton.type=joystick_button
tinkerforge:joystickbutton.tactile=True

tinkerforge:yposition.uid=aXJ
tinkerforge:yposition.subid=joystick_yposition
tinkerforge:yposition.type=joystick_yposition
```

##### Items file entry (e.g. tinkerforge.items):
```
Number XPostion              "XPostion [%.0f]" { tinkerforge="uid=<your_uid>, subid=joystick_xposition"}
Number YPostion              "YPostion [%.0f]" { tinkerforge="name=yposition"}
Contact JoystickButton       "Button" { tinkerforge="uid=<your_uid>, subid=joystick_button"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf_weather label="Joystick"
{
  Frame {
	Text item=XPostion
	Text item=YPostion
	Text item=JoystickButton
	}
}
```
---

### LCD 20x4 Display Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/LCD_20x4.html)

#### Binding properties:

The LCD20x4 is a bit special as it acts as actuator which can receive text messages. To
achieve this, you have to configure the device as String item.

What’s the meaning of this magic string?
```
sendCommand(TF_LCD, String::format("TFNUM<213>%4s"Barometer.state.format("%d")
                      ))
```
TFNUM is just a flag to signal the binding that some position information is passed. The first
number is the line number, starting from 0. The second and third number are interpreted as the
position in the line, starting from 0.

The above example would write the current value of the barometer bricklet to line 3 starting from
position 14, with a fixed width of 4 (this is because of %4s).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_LCD20x4 |

##### Backlight sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | backlight |
| type | openHAB type name | backlight |

##### Button sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | button0, button1, button2, button3 |
| type | openHAB type name | lcd_button |


##### Items file entry (e.g. tinkerforge.items):
```
String LCD         "LCD" { tinkerforge="uid=<your_uid>"}
Switch LCDBacklight        "LCDBacklight" { tinkerforge="uid=<your_uid>, subid=backlight"}
Switch Button0         "Button0" { tinkerforge="uid=<your_uid>, subid=button0"}
Switch Button1         "Button1" { tinkerforge="uid=<your_uid>, subid=button1"}
Switch Button2         "Button2" { tinkerforge="uid=<your_uid>, subid=button2"}
Switch Button3         "Button3" { tinkerforge="uid=<your_uid>, subid=button3"}
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Switch item=LCDBacklight
```
##### Rules file (e.g. tinkerforge.rules):
```
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
```

---

### LED Strip Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/LED_Strip.html)

#### Binding properties:

##### openhab.cfg:
```
tinkerforge:ledstrip.uid=jGu
tinkerforge:ledstrip.type=bricklet_ledstrip
tinkerforge:ledstrip.frameduration=100
tinkerforge:ledstrip.chiptype=ws2801
tinkerforge:ledstrip.clockfrequency=1000000
tinkerforge:ledstrip.colorMapping=rbg
tinkerforge:ledstrip.subDevices=ledgroup1

tinkerforge:ledgroup1.uid=jGu
tinkerforge:ledgroup1.subid=ledgroup1
tinkerforge:ledgroup1.type=ledgroup
tinkerforge:ledgroup1.leds=0|1-6
```

##### Items file entry (e.g. tinkerforge.items):
```
Color  tfled1   <slider> {tinkerforge="uid=<your_uid>, subid=ledgroup1"}
Color  tfled2   <slider> {tinkerforge="uid=<your_uid>, leds=7-14, colorMapping=rbg"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="TinkerForge LED"
{
    Frame label="LED Strip" {
        Colorpicker item=tfled1 icon="slider"
        Colorpicker item=tfled2 icon="slider"
    }
}

```
---

### Linear Poti Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Linear_Poti.html)

#### Binding properties:

There is one device called bricklet_linear_poti. You can expect values from 0 - 100.
The default callback period is 10 millis, you can change this within openhab.cfg.

##### openhab.cfg:
```
tinkerforge:linearpoti.uid=egW
tinkerforge:linearpoti.type=bricklet_linear_poti
tinkerforge:linearpoti.callbackPeriod=1000
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Poti              "Poti [%.0f]" { tinkerforge="uid=<your_uid>"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tflabel="Linear Poti"
{
  Frame {
	Text item=Poti
	}
}
```
---

### Load Cell Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Load_Cell.html)

#### Binding properties:

##### openhab.cfg:
```
tinkerforge:weight.uid=v8V
tinkerforge:weight.type=bricklet_loadcell
tinkerforge:weight.callbackPeriod=100
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Weight              "Weight [%.0f]" { tinkerforge="uid=<your_uid>"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="Load Cell"
{
  Frame {
	Text item=Weight
	}
}
```
---

### Motion Detector Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Motion_Detector.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | motion_detector |

##### openhab.cfg:
```
tinkerforge:motion.uid=<your_uid>
tinkerforge:motion.type=motion_detector
```

##### Items file entry (e.g. tinkerforge.items):
```
Contact motion      "motion [MAP(en.map):MOTION%s]" {tinkerforge="uid=<your_uid>"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=motion
```

##### en.map file:
```
MOTIONCLOSED=no motion
MOTIONOPEN=montion detected
```
---

### Multi Touch Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Multi_Touch.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust sensitivity, recalibrate, disable
electrodes or use a [_symbolic name_](#sym_name).

##### Bricklet:

##### Device configuration:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_multitouch |
| sensitivity | | value between 5-201 |
| recalibrate | recalibrate the sensor | true or false |

```
tinkerforge:touch.uid=<your_uid>
tinkerforge:touch.type=bricklet_multitouch
tinkerforge:touch.sensitivity=181
tinkerforge:touch.recalibrate=true
```

##### Electrode sub device configuration:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device|electrode0 electrode1 electrode2 electrode3 electrode4 electrode5 electrode6 electrode7 electrode8 electrode9 electrode10 electrode11|
| disableElectrode | disables the electrode | true or false|
```
tinkerforge:e1.uid=<your_uid>
tinkerforge:e1.type=electrode
tinkerforge:e1.subid=electrode1
tinkerforge:e1.disableElectrode=true
```

##### Proximity sub device configuration:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device|proximity|
| disableElectrode | disables the proximity detection | true or false|

##### openhab.cfg:
```
tinkerforge:prox.uid=<your_uid>
tinkerforge:prox.type=proximity
tinkerforge:prox.subid=proximity
tinkerforge:prox.disableElectrode=true
```

##### Items file entry (e.g. tinkerforge.items):
```
Contact electrode0      "electrode0 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode0"}
Contact electrode1      "electrode1 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode1"}
Contact electrode2      "electrode2 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode2"}
Contact electrode3      "electrode3 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode3"}
Contact electrode4      "electrode4 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode4"}
Contact electrode5      "electrode5 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode5"}
Contact electrode6      "electrode6 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode6"}
Contact electrode7      "electrode7 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode7"}
Contact electrode8      "electrode8 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode8"}
Contact electrode9      "electrode9 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode9"}
Contact electrode10     "electrode10 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode10"}
Contact electrode11     "electrode11 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=electrode11"}
Contact proximity       "proximity [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=proximity"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
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
---

### Moisture Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Moisture.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_moisture |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
```
tinkerforge:moisture.uid=<your_uid>
tinkerforge:moisture.type=bricklet_moisture
tinkerforge:moisture.threshold=0
tinkerforge:moisture.callbackPeriod=5000
tinkerforge:moisture.movingAverage=90
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Moisture                 "Moisture [%.1f]"  { tinkerforge="uid=<your_uid>" }
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=Moisture
```
---

### Piezo Speaker Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Piezo_Speaker.html)

#### Binding properties:

The Piezo Speaker Bricklet supports two modes:
  * sending morse codes with configurable frequency
  * sending tones with configurable duration and frequency

Configuration is done through item definition. In order to use different tone sequences you need to use one item per tone sequence.

Example:
```
Switch Beep      "Beep" { autoupdate="false", tinkerforge="uid=<your_uid>, mode=beep, durations=500|100, frequencies=10|10000, repeat=2" }
Switch Morse      "Morse" { autoupdate="false", tinkerforge="uid=<your_uid>, mode=morse, morsecodes=...---...|---, frequencies=10|10000, repeat=2" }
```
With the *repeat* statement the tone sequence is repeated with the given number.

---

### PTC Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/PTC.html)

#### Binding properties:

##### openhab.cfg:
```
tinkerforge:brickletptc.uid=rjC
tinkerforge:brickletptc.type=bricklet_ptc
tinkerforge:brickletptc.wiremode=2

tinkerforge:ptctemperature.uid=rjC
tinkerforge:ptctemperature.subid=ptc_temperature
tinkerforge:ptctemperature.type=ptc_temperature
tinkerforge:ptctemperature.callbackPeriod=1000
```

##### Items file entry (e.g. tinkerforge.items):
```
Contact connected {tinkerforge="uid=<your_uid>, subid=ptc_connected"}
Number temperature "Temperature [%.2f]" {tinkerforge="uid=<your_uid>, subid=ptc_temperature"}
Number resistance "Resistance [%.0f]" {tinkerforge="uid=<your_uid>, subid=ptc_resistance"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="TinkerForge PTC"
{
    Frame label="PTC" {
        Text item=temperature
        Text item=resistance
        Text item=connected
    }
}
```
---

### Remote Switch Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Remote_Switch.html)

#### Binding properties:

An entry in openhab.cfg is *mandatory*. You have to set sub device names $subdevice for your devices in the appropriate
type$typeDevices variable as space separated list. $type depends on the device hardware type of your switching device.
You must also add configuration for the $subdevice device. The available configuration variables depend
on the device type.

##### openhab.cfg:

* Device configuration

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

* Sub device type A configuration:

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

tinkerforge:rs_living_room2.uid=jKw
tinkerforge:rs_living_room2.subid=rslr2
tinkerforge:rs_living_room2.type=remote_switch_a
tinkerforge:rs_living_room2.houseCode=31
tinkerforge:rs_living_room2.receiverCode=9
```

* Sub device type B configuration:

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

* Sub device type C configuration:

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

##### Items file entry (e.g. tinkerforge.items):
```
Switch r0    "r0" <socket> (Lights)       {tinkerforge="uid=<your_uid>, subid=rslr1"}
Switch r1    "r1" <socket> (Lights)      {tinkerforge="uid=<your_uid>, subid=rslr2"}
Switch rb    "rb" <socket> (Lights)      {tinkerforge="uid=<your_uid>, subid=kitchen"}
Switch rc    "rc" <socket> (Lights)      {tinkerforge="uid=<your_uid>, subid=floor"}

Group:Switch:OR(ON,OFF)	Lights	"All Lights [(%d)]"
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="RemoteSwitch"
{
	Frame label="Group" {
		         Text label="Group Demo" icon="1stfloor" {
                Switch item=Lights mappings=[OFF="All Off",ON="All On"]
         }

	}
  Frame {
      Slider item=rb
			Switch item=r0
			Switch item=r1
			Switch item=rb
			Switch item=rc
        }
}

```
---

### Rotary Encoder Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Rotary_Encoder.html)

#### Binding properties:

There are two sub devices: encoder and button.
Callback period for encoder defaults to 10 milli seconds. CallbackPeriod can be configured
for the encoder sub device.

##### Button
Two operating modes for the button. The button can behave like a switch or
like a tactile switch.  
* Switch mode
The switch mode operates like this: pressing the button toggles the
switch state, if state was ON it goes to OFF and vice versa. Releasing the button doesn't
change anything, only the next button press will change the state.
* Tactile switch mode
Pressing the button changes the switch state to ON and releasing the button changes the
state back to OFF again.

Switch Mode is the default mode you can change the mode to tactile by adding a line like this to your openhab.cfg:
```
tinkerforge:button.tactile=True
```

##### openhab.cfg:
```
tinkerforge:encoder.uid=kHv
tinkerforge:encoder.subid=encoder
tinkerforge:encoder.type=rotary_encoder
tinkerforge:encoder.callbackPeriod=1

tinkerforge:button.uid=kHv
tinkerforge:button.subid=button
tinkerforge:button.type=rotary_encoder_button
tinkerforge:button.tactile=False
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Counter "Counter [%d]" { tinkerforge="uid=<your_uid>, subid=encoder"}
Contact Button "Button" { tinkerforge="uid=<your_uid>, subid=button"}
Switch Clear "Clear"
```

##### Rules (e.g tinkerforge.rules):
```
import org.openhab.core.library.types.*

rule "Clear"
when Item Clear changed
then
	tfRotaryEncoderClear("<your_id>")
end
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="RotaryEncoder"
{
  Frame {
	Text item=Counter
	Text item=Button
	Switch item=Clear
	}
}
```
---

### Segment Display 4x7 Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Segment_Display_4x7.html)

#### Binding properties:

The Segment Display 4x7 is a bit special as it acts as actuator which can receive number messages. To
achieve this, you have to configure the device as Number item.

An entry in openhab.cfg is only needed if you want to use a [_symbolic name_](#sym_name).

##### Items file entry (e.g. tinkerforge.items):
```
Number Segment7         "Segment7" { tinkerforge="uid=<your_uid>"}
```

##### Rules file (e.g. tinkerforge.rules):

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
---

### Solid State Relay Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Solid_State_Relay.html)

#### Binding properties:

 * no sub devices
 * no configuration needed for openhab.cfg

##### Items file entry (e.g. tinkerforge.items):
```
Switch relay "Relay" {tinkerforge="uid=<your_uid>"}
Contact relaystatus "Relay Status"
```

##### Rules file (e.g. tinkerforge.rules):
```
import org.openhab.core.library.types.*

rule "change every 5 seconds"
when
    Time cron "0/1 * * * * ?"
then
    if(relay.state == ON)
        sendCommand(relay, OFF)
    else
        sendCommand(relay, ON)
end

rule "Relay Status"
when
    Item relay changed
then
    if (relay.state == ON)
    postUpdate(relaystatus, CLOSED)
    else
    postUpdate(relaystatus, OPEN)
end
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="TinkerForge SolidStateRelay"
{
    Frame label="Relay" {
        Switch item=relay
        Text item=relaystatus
    }
}
```

### Sound Intensity Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Sound_Intensity.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_soundintensity |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:sound.uid=<your_uid>
tinkerforge:sound.type=bricklet_soundintensity
tinkerforge:sound.threshold=1
tinkerforge:sound.callbackPeriod=5000
```
##### Items file entry (e.g. tinkerforge.items):
```
Number SoundIntensity                 "Sound [%.1f]"  { tinkerforge="uid=<your_uid>" }
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=SoundIntensity
```
---

### Temperature Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Temperature.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

Since OH 1.8 there is a new option slowI2C which could be set to "True" or "False",
the default value is "False". More information on this setting can be found here:
http://www.tinkerforge.com/en/doc/Software/Bricklets/Temperature_Bricklet_Java.html#advanced-functions


| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_temperature |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:temperature.uid=7Dj
tinkerforge:temperature.type=bricklet_temperature
tinkerforge:temperature.slowI2C=False
```

##### Items file entry (e.g. tinkerforge.items):
```
Number temperature "Temperature [%.2f]" {tinkerforge="uid=<your_uid>"}
```
##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="TinkerForge Temperature"
{
    Frame label="Temperature" {
        Text item=temperature
    }
}
```
---

### Temperature IR Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Temperature_IR.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh),
if you want to use a [_symbolic name_](#sym_name) or adjust the emissivity of the object temperature device.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_barometer |

##### Object temperature sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | object_temperature |
| type | openHAB type name | object_temperature |
| emissivity |  emissivity that is used to calculate the surface temperature | a factor of 65535 e.g. 6553|
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

###### openhab.cfg:
```
tinkerforge:objIR.uid=<your_uid>
tinkerforge:objIR.subid=object_temperature
tinkerforge:objIR.type=object_temperature
tinkerforge:objIR.emissivity=65535
tinkerforge:objIR.threshold=0
```

##### Ambient temperature sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | ambient_temperature |
| type | openHAB type name | ambient_temperature |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

###### openhab.cfg:
```
tinkerforge:ambIR.uid=<your_uid>
tinkerforge:ambIR.subid=ambient_temperature
tinkerforge:ambIR.type=ambient_temperature
tinkerforge:ambIR.threshold=0
```
##### Items file entry (e.g. tinkerforge.items):
```
Number AmbientTemperature "AmbientTemperature [%.1f C]"  { tinkerforge="uid=<your_uid>, subid=ambient_temperature" }
Number ObjectTemperature "ObjectTemperature [%.1f C]"  { tinkerforge="uid=<your_uid>, subid=object_temperature" }
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=AmbientTemperature
Text item=ObjectTemperature
```

---

### Tilt Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Tilt.html)

#### Binding properties:

You can use a contact, number or switch item.

An entry in openhab.cfg is only needed if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_tilt |

##### openhab.cfg:
```
tinkerforge:tilt.uid=<your_uid>
tinkerforge:tilt.type=bricklet_tilt
```

##### Items file entry (e.g. tinkerforge.items):
```
Contact tiltContact     "tilt [MAP(en.map):%s]" { tinkerforge="uid=<your_uid>" }
Number tiltSensor       "tilt [MAP(en.map):%s]"  { tinkerforge="uid=<your_uid>" }
Switch tiltSwitch         "tilt" { tinkerforge="uid=<your_uid>" }
```

##### Sitemap file entry (e.g tinkerforge.sitemap):

```
Text item=tiltContact
Text item=tiltSensor
Switch item=tiltSwitch
```

##### en.map file entry (optional):
```
0=closed
1=open
2=vibrating
```

---

### Voltage/Current Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Voltage_Current.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh),
if you want to use a [_symbolic name_](#sym_name) or adjust the averaging, voltage conversion time,
current conversion time of the device.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_voltageCurrent |
|averaging|number of averages|0-7|
|voltageConversionTime|voltage conversion time|0-7|
|currentConversionTime|current conversion time|0-7|
```
tinkerforge:voltageCurrent.uid=<your_uid>
tinkerforge:voltageCurrent.type=bricklet_voltageCurrent
tinkerforge:voltageCurrent.averaging=3
tinkerforge:voltageCurrent.voltageConversionTime=4
tinkerforge:voltageCurrent.currentConversionTime=4
```

##### Voltage sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | voltageCurrent_voltage |
| type | openHAB type name | voltageCurrent_voltage |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

###### openhab.cfg:
```
tinkerforge:vc_voltage.uid=<your_uid>
tinkerforge:vc_voltage.subid=voltageCurrent_voltage
tinkerforge:vc_voltage.type=voltageCurrent_voltage
tinkerforge:vc_voltage.threshold=20
tinkerforge:vc_voltage.callbackPeriod=100
```

##### Current sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | voltageCurrent_current |
| type | openHAB type name | voltageCurrent_current |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

###### openhab.cfg:
```
tinkerforge:vc_current.uid=<your_uid>
tinkerforge:vc_current.subid=voltageCurrent_current
tinkerforge:vc_current.type=voltageCurrent_current
tinkerforge:vc_current.threshold=10
tinkerforge:vc_current.callbackPeriod=100
```

##### Power sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | voltageCurrent_power |
| type | openHAB type name | voltageCurrent_power |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

###### openhab.cfg:
```
tinkerforge:vc_power.uid=<your_uid>
tinkerforge:vc_power.subid=voltageCurrent_power
tinkerforge:vc_power.type=voltageCurrent_power
tinkerforge:vc_power.threshold=10
tinkerforge:vc_power.callbackPeriod=100
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Voltage       "Voltage [%d mV]"  { tinkerforge="uid=<your_uid>, subid=voltageCurrent_voltage" }
Number Current       "Current [%d mA]"  { tinkerforge="uid=<your_uid>, subid=voltageCurrent_current" }
Number Power         "Power [%d mW]"  { tinkerforge="uid=<your_uid>, subid=voltageCurrent_power" }
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
Text item=Voltage
Text item=Current
Text item=Power
```

---

### Weatherstation Kit

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Kits/WeatherStation/WeatherStation.html#starter-kit-weather-station)

#### Configuration examples:

##### openhab.cfg:
```
tinkerforge:lcdbutton2.uid=d4j
tinkerforge:lcdbutton2.subid=button2
tinkerforge:lcdbutton2.type=lcd_button
tinkerforge:lcdbutton2.tactile=True

tinkerforge:lcdbutton3.uid=d4j
tinkerforge:lcdbutton3.subid=button3
tinkerforge:lcdbutton3.type=lcd_button
tinkerforge:lcdbutton3.tactile=True
```

##### Items file entry (e.g. tinkerforge.items):
```
Number TF_Humdity                 "Humidity [%.1f %%]"  { tinkerforge="uid=<your_uid>" }
Number TF_Barometer               "Pressure [%.1f mBar]" { tinkerforge="uid=<your_uid>" }
Number TF_Barometer_Temperature   "Temperature [%.1f °C]" { tinkerforge="uid=<your_uid>, subid=temperature" }
Number TF_AmbientLight            "Luminance [%.1f Lux]" { tinkerforge="uid=<your_uid>" }

String TF_LCD         "LCD" { tinkerforge="uid=<your_uid>"}
Switch TF_LCDBacklight        "LCDBacklight" { tinkerforge="uid=<your_uid>, subid=backlight"}
Switch TF_Button0         "Button0" { tinkerforge="uid=<your_uid>, subid=button0"}
Switch TF_Button1         "Button1" { tinkerforge="uid=<your_uid>, subid=button1"}
Switch TF_Button2         "Button2" { tinkerforge="uid=<your_uid>, subid=button2"}
Switch TF_Button3         "Button3" { tinkerforge="uid=<your_uid>, subid=button3"}
```

##### Rules (e.g tinkerforge.rules):
```
import org.openhab.core.library.types.*

var Integer initialSleepTime = 10

rule "Weatherstation LCD init from Backlight"
when
    Item TF_LCDBacklight changed or System started
then
    createTimer(now.plusSeconds(initialSleepTime)) [|
        sendCommand(TF_LCD, "TFNUM<00>Temperature:")
        sendCommand(TF_LCD, "TFNUM<019>C")
        sendCommand(TF_LCD, "TFNUM<10>Humidity   :")
        sendCommand(TF_LCD, "TFNUM<119>%")
        sendCommand(TF_LCD, "TFNUM<20>Pressure   :")
        sendCommand(TF_LCD, "TFNUM<217>hPa")
        sendCommand(TF_LCD, "TFNUM<30>Luminance  :")
        sendCommand(TF_LCD, "TFNUM<317>Lux")
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

rule "Goodbye"
when
        System shuts down
then
        sendCommand(TF_LCDBacklight, OFF)
end

rule "Weatherstation LCD Backlight"
        when
                Item TF_Button0 changed
        then
        if (TF_LCDBacklight.state == ON)
            sendCommand(TF_LCDBacklight, OFF)
        else
            sendCommand(TF_LCDBacklight, ON)

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
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf_weather label="Tinkerforge Weather Station"
{
	  Frame {
	  		Text item=TF_Humdity
				Text item=TF_Barometer
				Text item=TF_Barometer_Temperature
				Text item=TF_AmbientLight
				Switch item=TF_LCDBacklight
				Switch item=TF_Button0
				Switch item=TF_Button1
				Switch item=TF_Button2
				Switch item=TF_Button3
			}
}
```
---

### Brick Daemon

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Industrial_Quad_Relay.html)

#### Binding properties:

##### Items file entry (e.g. tinkerforge.items):
```
Contact brickd1connected {tinkerforge="<uid=<your_ip>:<your_port>, subid=connected"}
Number brickd1counter "Brickd1 [%d]" {tinkerforge="uid=<your_ip>:<your_port>, subid=connected_counter"}
```

##### Sitemap file entry (e.g. tinkerforge.sitemap):
```
sitemap led label="TinkerForge Brickd"
{
    Frame label="Brickd" {
        Text item=brickd1connected
        Text item=brickd1counter
    }
}
```
##### openhab.cfg:
```
tinkerforge:hosts=<your_ip>:<your_port>
tinkerforge:refresh=1000
```

---

## News

---

## Red Brick

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricks/RED_Brick.html#red-brick)

openHAB is preinstalled on the RED Brick image and can be configured with the TinkerForge BrickViewer.

---

## Tinkerforge Actions
The new openHAB action [[TinkerForgeAction|Actions]] comes up with the actions tfServoSetposition, tfClearLCD and tfDCMotorSetspeed.
tfServoSetposition(uid, num, position, velocity, acceleration) can be used to control the [Servo Brick](#servo-brick).
tfClearLCD(uid) uid is the uid of the LCD display. A call of tfClearLCD will clear the [LCD 20×4 Display Bricklet](#lcd-20x4-display-bricklet).
tfDCMotorSetspeed(String uid, String speed, String acceleration, String drivemode) can be used to control a DC motor through the [DC Brick](#dc-brick).

  Example:

  ```
  tfServoSetposition("6Crt5W", "servo0", "-9000", "65535", "65535")
  ```

---

## Upgrading
### Upgrading from 1.3
- LCDBacklight now is a sub device of LCD20x4 Bricklet (items file has to be changed)
- LCD20x4Button now posts an update not a command anymore (rules has to be changed)
- IndustrialQuadRelay sub id numbering now starts from zero (items file has to be changed)

---

### Upgrading from 1.4
- Threshold values now have the same unit as the sensor value (incompatible change, you may have to update your openhab.cfg).
   - Background:
     Some getters for sensor values of the TinkerForge API return higher precision values by using short values with fractions of the common units, e.g. the Temperature Bricklet returns hundredths of a celsius degree. The binding converts these values to common units using a BigDecimal. Until now the threshold values were applied to the sensor value before this conversion. Because of that the threshold values had to be given as the appropriate fraction. With the drawback that the openHAB users need some knowledge about the behavior of the TinkerForge API. Now the threshold is applied after converting the original values. Therefore the units used for the sensor values and the threshold values are equal.
    - Humidity Bricklet
        - calculate new threshold values from values of your current configuration: divide by 10
        - unity: relative humidity in percent
    - Distance IR Bricklet
       - calculate new threshold values from values of your current configuration: nothing changed
       -  unity: millimeter
    - Temperature Bricklet
       - calculate new threshold values from values of your current configuration: divide by 100
       - unity: degree Celsius
    - Barometer Bricklet
       - calculate new threshold values from values of your current configuration: divide by 1000
       - unity: mbar
    - Ambient Light Bricklet
       - calculate new threshold values from values of your current configuration: divide 10
       - unity: Lux

---

### Upgrading from 1.7
- New Devices
 - Joystick Bricklet
 - Linear Poti Bricklet
 - Dual Button Bricklet
 - PTC
 - Industrial Dual 0-20mA
 - Solid State Relay

- New Features
 - Tinkerforge Action Addon
 - Brick DC fully supported
 - Brick Servo fully supported
 - Authentication support for brickd
 - Tactile feature for Dualbutton, LCD Buttons, Joystick Button.
 - LED Strip: sub devices and switching capabilities, configurable Frameduration, ChipType and Clockfrequency
 - Remote Switch dimmer support

- Bugfixes
 - Fix for configuration handling of device aliases

- Other changes
 - Updated Tinkerforge API to 2.1.4
 - [Example configurations](https://github.com/theoweiss/openhab-tinkerforge-configuration-examples) available on github

 ---

### Upgrading from 1.8

-- content

---
