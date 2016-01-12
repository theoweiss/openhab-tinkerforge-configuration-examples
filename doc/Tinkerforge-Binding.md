Documentation of the TinkerForge binding bundle

## Table of Contents
- [Introduction](#introduction)
- [Generic Item Binding Configuration](#generic-item-binding-configuration)
   - [Basic Configuration](#basic-configuration)
   - [Item Binding Configuration](#item-binding-configuration)
   - [Example Configuration](#example-configuration)
- [Advanced Configuration](#advanced-configuration)
   - [Overview](#overview)
   - [Callback and threshold](#callback-and-threshold)
   - [Refresh of Sensor Values](#refresh-of-sensor-values)
- [Supported Devices](#supported-devices)
  - Bricks
    - [DC Brick](#dc-brick)
    - [Servo Brick](#servo-brick)
  - Bricklets
    - [Accelerometer Bricklet](#accelerometer-bricklet)
    - [Ambient Light Bricklet 2.0](#ambient-light-bricklet-v2)
    - [Analog In Bricklet](#analog-in-bricklet)
    - [Analog In Bricklet 2.0](#analog-in-bricklet-20)
    - [Barometer Bricklet, barometer and temperature device](#barometer-bricklet)
    - [Color Bricklet](#color-bricklet)
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
    - [Industrial Dual Analog In Bricklet](#industrial-dual-analog-in-bricklet)
    - [Industrial Quad Relay Bricklet](#industrial-quad-relay-bricklet)
    - [IO 16 Bricklet](#io-16-bricklet)
    - [Joystick Bricklet](#joystick-bricklet)
    - [Laser Range Finder Bricklet](#laser-range-finder-bricklet)
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
  - [from Version 1.7.0](#upgrading-from-170)
  - [from Version 1.7.1](#upgrading-from-171)
  - [from Version 1.8.0](#upgrading-from-180)

---

## Introduction
[TinkerForge](http://www.tinkerforge.com) is a system of open source hardware building blocks that
allows you to combine sensor and actuator blocks by plug and play. You can create your individual
hardware system by choosing the necessary building blocks for your project and combine it with other
home automation products. There are many blocks available e.g for temperature, humidity or air pressure
measurement as well as for I/O, LCDs and motor control. You will find a complete List of available
blocks [here](http://www.tinkerforge.com/en/doc/Product_Overview.html).

This binding connects the [TinkerForge](http://tinkerforge.com) devices to the openHAB event bus.
Sensor values from devices are made available to openHAB and actions on devices can be triggered by
openHAB.

The binding supports the connection to several brickd instances.
The TinkerForge auto reconnect feature is supported. Furthermore even if the initial connect failed the binding will make retries to get connected to the brickd.

The TinkerForge binding bundle is available as a separate (optional) download. For installation of
the binding, please see Wiki page [[Bindings]].

---

## Generic Item Binding Configuration

### Basic Configuration
In order to connect openHAB to TinkerForge devices you need to define all the brickd hosts and ports in the openhab.cfg file.
The following properties must be configured to define a brickd connection:
```
tinkerforge:hosts=<IP address>[:port][:<secret>] ...
```
The properties indicated by '<...>' need to be replaced with an actual value. Properties surrounded
by square brackets are optional. Several brickd configurations are delimited by a space.

*port* and secret are optional values. You must specify the port if your brickd doesn't use the default port 4223.
*secret* must be specified if you use a brickd connection **with authentication**:  (see also [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Software/Brickd.html#brickd-authentication))

| <b>Property</b> | <b>Description</b> |
| --------------- | ------------------ |
| IP address | IP address of the brickd |
| port | The listening port of of the brickd (optional, default 4223) |
| secret | password used for authentication |

#### Example configuration

Authenticate with password 1234 and default port
```
tinkerforge:hosts=127.0.0.1::1234
```

Authenticate with password 1234 and individual port 4224
```
tinkerforge:hosts=127.0.0.1:4224:1234
```

You may also configure several different hosts.
For connecting several brickds, use multiple &lt;IP address&gt; statements delimited by a space.
```
tinkerforge:hosts=127.0.0.1:4224:1234 192.168.1.100::secret
```


#### Item Binding Configuration

In order to bind an item to a device, you need to provide configuration settings. The easiest way
to do so is to add binding information in your item file (in the folder '${openhab_home}/configurations/items').


The configuration of the TinkerForge binding item looks like this:
```
tinkerforge="(uid=<your_id> [, subid=<your_subid>] | name=<your_name>)"
```
The configuration is quite simple. You either have to set a value for the uid and optionally for the
subid of the device, or - if the device is configured in openhab.cfg - the "symbolic name" of the device.

| Property | Description |
| -------- | ----------- |
| uid      | TinkerForge uid of the device (Use the Brick Viewer to get this value) |
| subid    | optional subid of the device|
| name     | _symbolic name_ of the device. The name is only available if there is some configuration for the device in openhab.cfg. |

For additional configuration options see the appropriate device section.

---

### Advanced Configuration
There are several configuration parameters to control the behavior of the devices. The available
parameters depend on the device type.

#### Overview
For most of the devices **no configuration** is needed in openhab.cfg, they can be used with reasonable defaults.

<a name="sym_name"></a>
If you want to get rid of _uid_ and _subid_ statements in the items or rule file, you can use openhab.cfg to get a _symbolic name_.

A configuration line for a TinkerForge Device looks like this in openhab.cfg:
```
tinkerforge:<symbolic name>.<property>=<value>
```
The *symbolic name* string can be used in the items configuration as an alternative for the uid and subid values.

The following table lists the general available properties.

|<b>Property</b>|<b>Description</b>|<b>Device</b>|
|---------------|------------------|-------------|
|uid|TinkerForge uid of the device (use the Brick Viewer to get this value)|mandatory for all devices|
|type|the device type|mandatory for all devices|
|subid|  subid of the device, subid's are used if a brick/bricklet houses more then one device (e.g. the Dual Relay Bricklet)|mandatory for sub devices|

The following table shows the TinkerForge device, its device type, its subid and if callback is supported.


|<b>device</b>|<b>type name</b>|<b>subid(s)</b>|<b>Callback</b>|
|-------------|----------------|---------------|---------------|
|DC Brick|brick_dc|||
|Servo Brick sub devices|servo|servo[0-6]||
|Accelerometer Bricklet|bricklet_accelerometer|||
|Accelerometer Bricklet subdevice|accelerometer_direction|x|x|
|Accelerometer Bricklet subdevice|accelerometer_direction|y|x|
|Accelerometer Bricklet subdevice|accelerometer_direction|z|x|
|Accelerometer Bricklet subdevice|accelerometer_temperature|temperature||
|Accelerometer Bricklet subdevice|accelerometer_led|led||
|Ambient Light Bricklet 2.0|bricklet_ambient_lightv2||x|
|Analog In Bricklet|bricklet_analogin||x|
|Analog In Bricklet 2.0|bricklet_analoginv2||x|
|Barometer Bricklet|bricklet_barometer||x|
|Barometer Bricklet temperature sensor sub device|barometer_temperature|temperature||
|Color Bricklet|bricklet_color|||
|Color Bricklet subdevice|color|color-color|x|
|Color Bricklet subdevice|temperature|color_temperature|x|
|Color Bricklet subdevice|illuminance|color_illuminance|x|
|Distance IR Bricklet|bricklet_distance_ir||x|
|Dual Button Bricklet||||
|Dual Button Bricklet subdevice||dualbutton_leftled|x|
|Dual Button Bricklet subdevice||dualbutton_rightled|x|
|Dual Button Bricklet subdevice||dualbutton_leftbutton|x|
|Dual Button Bricklet subdevice||dualbutton_rightbutton|x|
|Dual Relay Bricklet sub devices|dual_relay|relay[1-2]||
|Dust Detector Bricklet|bricklet_dustdetector||x|
|Hall Effect Bricklet|bricklet_halleffect||x|
|Humidity Bricklet|bricklet_humidity||x|
|Industrial Digital In 4 Bricklet|bricklet_industrial_digital_4in|in[0-3]|x|
|Industrial Digital Out 4 Bricklet sub devices|bricklet_industrial_digital_4out|out[0-3]||
|Industrial Dual 0-20mA Bricklet|bricklet_industrialdual020ma|||
|Industrial Dual 0-20mA Bricklet subdevice|industrial020ma_sensor|sensor[0-1]|x|
|Industrial Dual Analog In Bricklet|bricklet_industrial_dual_analogin|||
|Industrial Dual Analog In Bricklet subdevice|industrial_dual_analogin_channel|channel[0-1]|x|
|Industrial Quad Relay Bricklet|industrial_quad_relay|relay[0-3]||
|IO-4 Bricklet|bricklet_io4|||
|IO-4 Bricklet sub devices, which should be used as input ports|io4sensor|in[0-3]|x|
|IO-4 Bricklet sub devices, which should be used as output ports|io4_actuator|out[0-3]||
|IO-16 Bricklet|bricklet_io16|||
|IO-16 Bricklet sub devices, which should be used as input ports|iosensor|in[ab][0-7]|x|
|IO-16 Bricklet sub devices, which should be used as output ports|io_actuator|out[ab][0-7]||
|Joystick Bricklet|bricklet_joystick|||
|Joystick Bricklet subdevice||joystick_xposition|x|
|Joystick Bricklet subdevice||joystick_yposition|x|
|Joystick Bricklet subdevice||joystick_button|x|
|Laser Range Finder Bricklet||bricklet_laser_range_finder||
|Laser Range Finder Bricklet subdevice|laser_range_finder_distance|distance|x|
|Laser Range Finder Bricklet subdevice|laser_range_finder_velocity|velocity|x|
|Laser Range Finder Bricklet subdevice|laser_range_finder_laser|laser||
|LCD20x4 Bricklet|bricklet_LCD20x4|||
|LCD20x4 backlight|backlight|backlight||
|LCD20x4 Bricklet button sub devices|lcd_button|button[0-3]|interrupt|
|LED Strip Bricklet|bricklet_ledstrip|||
|LED Strip Bricklet subdevice|ledgroup|ledgroup[1-x]|x|
|Linear Poti Bricklet|bricklet_linear_poti||x|
|Load Cell Bricklet|bricklet_loadcell|||
|Load Cell Bricklet subdevice|weight|loadcell_weight|x|
|Load Cell Bricklet subdevice|led|loadcell_led|x|
|Motion Detector Bricklet|motion_detector||x|
|Multi Touch Bricklet|bricklet_multitouch|||
|Multi Touch Bricklet electrodes|electrode|electrode[0-11]|x|
|Moisture Bricklet|bricklet_moisture||x|
|Pieco Speaker Bricklet||||
|PTC Bricklet|bricklet_ptc|||
|PTC Bricklet subdevice||ptc_temperature|x|
|PTC Bricklet subdevice||ptc_connected|x|
|PTC Bricklet subdevice||ptc_resistance|x|
|Remote Switch Bricklet|bricklet_remote_switch|configurable||
|Remote Switch Bricklet sub devices|remote_switch_a or remote_switch_b or remote_switch_c|from configuration||
|Rotary Encoder Bricklet||||
|Rotary Encoder Bricklet subdevice|encoder|rotary_encoder|x|
|Rotary Encoder Bricklet subdevice|button|rotary_encoder_button|x|
|Segment Display 4x7 Bricklet||||
|Solid State Relay Bricklet||||
|Sound Intensity Bricklet|bricklet_soundintensity|||
|Temperature Bricklet|bricklet_temperature||x|
|TemperatureIR Bricklet|bricklet_temperatureIR|||
|TemperatureIR Bricklet sub device object temperature|object_temperature||x|
|TemperatureIR Bricklet sub device ambient temperature|ambient_temperature||x|
|Tilt Bricklet|bricklet_tilt|||
|Voltage Current Bricklet|bricklet_voltageCurrent|||

---

<a name="call_thresh"></a>
#### Callback and Threshold

The TinkerForge CallbackListeners - if available - are used to observe the sensor values of the
devices. These listeners are configured to update sensor values at a given time period
(callbackPeriod). The default configuration sets the **callbackPeriod** to 1 second. This value can
be changed in openhab.cfg. The values must be given in milliseconds.

The callbackPeriod controls the amount of traffic from the TF hardware to the binding.

In addition to the Callback a **threshold value** can be configured. This threshold means that even
if the listener reports a changed value, the value is only send to the openHAB eventbus if: the
difference between the last value and the current value is bigger than the threshold value. You can
think of it as a kind of hysteresis, it dampens the oscillation of openHAB item values.

The threshold controls the amount of  traffic from the binding to the openHAB eventbus.
Threshold values have the same unit as sensor values, no conversion is needed.

---

#### Refresh of Sensor Values

Devices which do not support callbacks will be polled with a configurable interval, the default
 is 60000 milliseconds. This value can be changed in openhab.cfg:
 
```
tinkerforge.refresh=<value in milliseconds>
```
---

## Supported Devices

### DC Brick

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricks/DC_Brick.html#dc-brick)

#### Binding properties:
The device supports Dimmer, Rollershutter and Number items.  Besides that the speed
can be set using a percent value.
The number items show the current velocity. The values are reported using the VelocityListener.
"[callbackPeriod](#call_thresh)" and "[threshold](#call_thresh)" for the listener can be configured in openhab.cfg.

* callbackPeriod: milliseconds
* threshold: numeric value

The item configuration options are:
* speed: the target speed (Switch)
* max: the maximum speed (Dimmer, Rollershutter)
* min: the minimum speed (Dimmer, Rollershutter)
* step: the step value for increasing decreasing speed (Dimmer)
* leftspeed: the speed when the left rollershutter controller is pressed or command "DOWN" was send
* rightspeed: the speed when the right rollershutter controller is pressed or command "UP" was send
* acceleration: acceleration overrides value from openhab.cfg
* drivemode: drivemode overrides value from openhab.cfg

##### openhab.cfg:
Values for acceleration and drivmode are default values and may be overridden by item definition.

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
       tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
end

```

---

### Servo Brick

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricks/Servo_Brick.html#servo-brick)

#### Binding properties:
The device supports Dimmer, Rollershuter and Number items. Besides that the speed
can be set using a percent value.

Number items will show the current position.

The item configuration options are:
* velocity: the velocity used to reach the new position
* max: the maximum position (Dimmer, Rollershutter)
* min: the minimum position (Dimmer, Rollershutter)
* step: the step value for increasing decreasing position (Dimmer)
* leftposition: the target position to reach when the left rollershutter controller is pressed or command "DOWN" was send
* rightposition: the target position to reach when the right rollershutter controller is pressed or command "UP" was send
* acceleration: the acceleration

##### TinkerForge Action
The openHAB action [TinkerforgeAction](#tinkerforge-actions) comes up with the action tfServoSetposition.
tfServoSetposition(uid, num, position, velocity, acceleration) can be used to control the servo.

###### Example
```
tfServoSetposition("<your_uid>", "servo0", "-9000", "65535", "65535")
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

### Accelerometer Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Accelerometer.html)

#### Binding properties:

The bricklet returns the acceleration in x, y and z direction. The values are given in g/1000 (1g = 9.80665m/s²).  
Bricklet temperature can be measured in degrees Celsius, LED can be turned on to indicate a specific acceleration was reached.  
Decreasing data rate or full scale range will also decrease the noise on the data.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_accelerometer |
| dataRate | sets the data rate, default=6 (100Hz) |0: off, 1: 3hz, 2: 6hz, 3: 12hz, 4: 25hz, 5: 50hz, 6: 100hz, 7: 400hz, 8: 800hz, 9: 1600hz|
| fullScale | sets the scale rate, default=1 (-4g to +4g) |0: 2g, 1: 4g, 2: 6g, 3: 8g, 4: 16g|
| filterBandwidth | sets the filter bandwidth, default=2 (200Hz) | 0: 800hz, 1: 400hz, 2: 200hz, 3: 50hz|

##### Accelerometer Bricklet sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | x, y, z, temperature, led |
| type | openHAB type name | accelerometer_direction, accelerometer_temperature, accelerometer_led|
| callbackPeriod | | see "Callback and Threshold" |

Note: Subdevices accelerometer_temperature and accelerometer_led don't support callbackPeriod!


##### openhab.cfg:
```
tinkerforge:accelerometer.uid=<your_uid>
tinkerforge:accelerometer.type=bricklet_accelerometer
tinkerforge:accelerometer.dataRate=6
tinkerforge:accelerometer.fullScale=1
tinkerforge:accelerometer.filterBandwidth=2

tinkerforge:ax.uid=<your_uid>
tinkerforge:ax.subid=x
tinkerforge:ax.type=accelerometer_direction
tinkerforge:ax.callbackPeriod=10
tinkerforge:ax.threshold=0

tinkerforge:ay.uid=<your_uid>
tinkerforge:ay.subid=y
tinkerforge:ay.type=accelerometer_direction
tinkerforge:ay.callbackPeriod=10
tinkerforge:ay.threshold=0

tinkerforge:az.uid=<your_uid>
tinkerforge:az.subid=z
tinkerforge:az.type=accelerometer_direction
tinkerforge:az.callbackPeriod=10
tinkerforge:az.threshold=0

tinkerforge:a_temperature.uid=<your_uid>
tinkerforge:a_temperature.subid=temperature
tinkerforge:a_temperature.type=accelerometer_temperature

tinkerforge:a_led.uid=<your_uid>
tinkerforge:a_led.subid=led
tinkerforge:a_led.type=accelerometer_led
```

##### Items file entry (e.g. tinkerforge.items):
```
Number X "X [%.3f]" {tinkerforge="uid=<your_uid>, subid=x"}
Number Y "Y [%.3f]" {tinkerforge="uid=<your_uid>, subid=y"}
Number Z "Z [%.3f]" {tinkerforge="uid=<your_uid>, subid=z"}
Number temperature "Temperature [%.2f °C]" {tinkerforge="uid=<your_uid>, subid=temperature"}
Switch led "Led"  {tinkerforge="uid=<your_uid>, subid=led"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="Accelerometer"
{
    Frame label="Accelerometer" {
        Text item=X
        Text item=Y
        Text item=Z
        Text item=temperature
        Switch item=led
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
| type | openHAB type name | bricklet_ambient_lightv2 |
| illuminanceRange | sets the illuminance range, default=8000lux | 0=64000lux, 1=32000lux, 2=16000lux, 3=8000lux, 4=1300lux, 5=600lux, 6=unlimited range |
| integrationTime | sets the integration time, default=200ms | 0=50ms, 1=100ms, 2=150ms, 3=200ms, 4=250ms, 5=300ms, 6=350ms, 7=400ms|
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:ambientlightv2.uid=<your_uid>
tinkerforge:ambientlightv2.type=bricklet_ambient_lightv2
tinkerforge:ambientlightv2.illuminanceRange=3
tinkerforge:ambientlightv2.integrationTime=3
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

### Analog In Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Analog_In.html)

#### Binding properties:

If property $range is set to 0, the device switches between the measurement ranges automatically. Set $range to 1-5 to manually switch between measurement ranges.

#### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_analogin |
| range | sets the measurement range, default=0 (automatically switched) | 1: 0V - 6.05V, 2: 0V - 10.32V, 3: 0V - 36.30V, 4: 0V - 45.00V, 5: 0V - 3.3V with ~0.81mV resolution |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |


##### openhab.cfg:
```
tinkerforge:ain.uid=<your_uid>
tinkerforge:ain.type=bricklet_analogin
tinkerforge:ain.range=0
tinkerforge:ain.callbackPeriod=1000
tinkerforge:ain.threshold=0
```

##### Items file entry (e.g. tinkerforge.items):
```
Number voltage "Voltage [%.0f mV]" {tinkerforge="uid=<your_uid>"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="Analog In"
{
    Frame label="Analog In" {
        Text item=voltage
    }
}
```
---

### Analog In Bricklet 2.0

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Analog_In_V2.html)

#### Binding properties:

Moving average is a calculation to analyze data points by creating series of averages of different subsets of the full data set.
Property $movingAverage sets the length of a moving averaging for the measured voltage, default is 50, $movingAverage=1 turns averaging off. With less averaging, there is more noise on the data.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_analoginv2 |
| movingAverage | sets the length of a moving averaging, default=50 | 1: averaging off, 2-50: averaging is 2-50 |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:ainv2.uid=<your_uid>
tinkerforge:ainv2.type=bricklet_analoginv2
tinkerforge:ainv2.movingAverage=50
tinkerforge:ainv2.callbackPeriod=1000
tinkerforge:ainv2.threshold=0
```

##### Items file entry (e.g. tinkerforge.items):
```
Number voltage "Voltage [%.0f mV]" {tinkerforge="uid=<your_uid>"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="Analog In V2"
{
    Frame label="Analog In V2" {
        Text item=voltage
    }
}
```
---

### Barometer Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Barometer.html)

#### Binding properties:

Bricklet measures air pressure in range of 10 to 1200mbar with a resolution of 0.012mbar.  
An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_barometer |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### Temperature sub device:

Subdevice property $barometer_temperature returns the temperature of the air pressure sensor which is internally used for temperature compensation of the air pressure measurement.
The temperature sub device does not support callbackPeriod, it will be polled. The polling interval can be configured using tinkerforge:refresh property.

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

### Color Bricklet
Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Color.html)

#### Binding properties:

Increasing the gain enables the sensor to detect a color from a higher distance.
The integration time provides a trade-off between conversion time and accuracy. With a longer integration time the values read will be more accurate but it will take longer time to get the conversion results.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_color |
| gain | sets the gain, derault=60x | 0: 1x gain, 1: 4x gain, 2: 16x gain, 3: 60x gain  |
| integrationTime | sets the integration time, default=154ms | 0: 2.4ms, 1: 24ms, 2: 101ms, 3: 154ms, 4: 700ms |

##### Color Bricklet sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | color, temperature, illuminance |
| type | openHAB type name | color_color, color_temperature, color_illuminance |
| callbackPeriod | |see "Callback and Threshold" |

Note: It is not possible to set the property $threshold for the Color Bricklet subdevices! 

##### openhab.cfg:
```
tinkerforge:brickletcolor.uid=<your_uid>
tinkerforge:brickletcolor.type=bricklet_color
tinkerforge:brickletcolor.gain=3
tinkerforge:brickletcolor.integrationTime=3

tinkerforge:color_color.uid=<your_uid>
tinkerforge:color_color.subid=color
tinkerforge:color_color.type=color_color
tinkerforge:color_color.callbackPeriod=1000

tinkerforge:color_temperature.uid=<your_uid>
tinkerforge:color_temperature.subid=temperature
tinkerforge:color_temperature.type=color_temperature
tinkerforge:color_temperature.callbackPeriod=1000

tinkerforge:color_illuminance.uid=<your_uid>
tinkerforge:color_illuminance.subid=illuminance
tinkerforge:color_illuminance.type=color_illuminance
tinkerforge:color_illuminance.callbackPeriod=1000
```

##### Items file entry (e.g. tinkerforge.items):
```
Color color "Color" {tinkerforge="uid=<your_uid>, subid=color"}
Number temperature "ColorTemperature [%.2f]" {tinkerforge="uid=<your_uid>, subid=temperature"}
Number illuminance "ColorIlluminance [%.0f]" {tinkerforge="uid=<your_uid>, subid=illuminance"}
Switch led "Color Led"  {tinkerforge="uid=<your_uid>, subid=led"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="Color"
{
    Frame label="Color" {
    	Colorpicker item=color
        Text item=temperature
        Text item=illuminance
        Switch item=led
    }
}
```
---

### Distance IR Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Distance_US.html)

#### Binding properties:

An entry in openhab.cfg is only needed if you want to adjust [threshold and / or callbackPeriod](#call_thresh) or if you want to use a [_symbolic name_](#sym_name).

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_distance_ir |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:

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

Distance is reported as unitless value, not in mm.

Moving average is a calculation to analyze data points by creating series of averages of different subsets of the full data set.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_distanceUS |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
| movingAverage | sets the moving average, default=20 | 0-100 |

##### openhab.cfg:
```
tinkerforge:distanceUS.uid=<your_uid>
tinkerforge:distanceUS.type=bricklet_distanceUS
tinkerforge:distanceUS.threshold=0
tinkerforge:distanceUS.callbackPeriod=100
tinkerforge:distanceUS.movingAverage=20
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

##### Dual Button Bricklet sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | dualbutton_leftled, dualbutton_rightled, dualbutton_leftbutton, dualbutton_rightbutton |
| type | openHAB type name | dualbutton_led, dualbutton_button |
| autotoggle | sets button autotoggle mode | True, False |
| tactile | sets switch mode | True, False |

##### openhab.cfg:
```
tinkerforge:led1.uid=<your_uid>
tinkerforge:led1.subid=dualbutton_leftled
tinkerforge:led1.type=dualbutton_led
tinkerforge:led1.autotoggle=True

tinkerforge:led2.uid=<your_uid>
tinkerforge:led2.subid=dualbutton_rightled
tinkerforge:led2.type=dualbutton_led
tinkerforge:led2.autotoggle=False

tinkerforge:button1.uid=<your_uid>
tinkerforge:button1.subid=dualbutton_leftbutton
tinkerforge:button1.type=dualbutton_button
tinkerforge:button1.tactile=False

tinkerforge:button2.uid=<your_uid>
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
tinkerforge:relay_coffee_machine.uid=<your_uid>
tinkerforge:relay_coffee_machine.type=dual_relay
tinkerforge:relay_coffee_machine.subid=relay1

tinkerforge:relay_garage_door.uid=<your_uid>
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

The measured dust density can be read out in µg/m³

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_dustdetector |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:dust.uid=<your_uid>
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

Bricklet can detect the presence of magnetic fields. It counts the (dis-)appearances of magnetic fields.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_halleffect |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
# really optional settings
tinkerforge:hall.uid=<your_uid>
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

The measured humidity can be read out directly in percent, no conversions are necessary.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_humidity |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:

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

 If you set the property $debouncePeriod to 100, you will get the interrupt maximal every 100ms. This is necessary if something that bounces is connected to the Digital In 4 Bricklet, such as a button.

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
Contact ID1 "ID1 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in0"}
Contact ID2 "ID2 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in1"}
Contact ID3 "ID3 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in2"}
Contact ID4 "ID4 [MAP(en.map):%s]" {tinkerforge="uid=<your_uid>, subid=in3"}
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

##### openhab.cfg
```
tinkerforge:brickletid020ma.uid=<your_uid>
tinkerforge:brickletid020ma.type=bricklet_industrialdual020ma

tinkerforge:temperature0.uid=<your_uid>
tinkerforge:temperature0.subid=sensor0
tinkerforge:temperature0.type=industrial020ma_sensor
tinkerforge:temperature0.callbackPeriod=100

tinkerforge:temperature1.uid=<your_uid>
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

### Industrial Dual Analog In Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Industrial_Dual_Analog_In.html)

#### Binding properties:

The property $sampleRate can be between 1 sample per second (SPS) and 976 samples per second. Decreasing the sample rate will also decrease the noise on the data.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_industrial_dual_analogin |
| sampleRate | sets the sample rate, default=6 (2 samples per second) | 0=976 SPS, 1=488 SPS, 2=244 SPS, 3=122 SPS, 4=61 SPS, 5=4 SPS, 6=2 SPS, 7=1 SPS |

##### Dual Analog In sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | channel0, channel1 |
| type | openHAB type name | industrial_dual_analogin_channel |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:diai.uid=<your_uid>
tinkerforge:diai.type=bricklet_industrial_dual_analogin
tinkerforge:diai.sampleRate=6

tinkerforge:channel0.uid=<your_uid>
tinkerforge:channel0.subid=channel0
tinkerforge:channel0.type=industrial_dual_analogin_channel
tinkerforge:channel0.callbackPeriod=1000
tinkerforge:channel0.threshold=0

tinkerforge:channel1.uid=<your_uid>
tinkerforge:channel1.subid=channel1
tinkerforge:channel1.type=industrial_dual_analogin_channel
tinkerforge:channel1.callbackPeriod=1000
tinkerforge:channel1.threshold=0
```

##### Items file entry (e.g. tinkerforge.items):
```
Number channel0 "Channel0 [%.0f mV]" {tinkerforge="uid=<your_uid>, subid=channel0"}
Number channel1 "Channel1 [%.0f mV]" {tinkerforge="uid=<your_uid>, subid=channel1"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="Industrial Dual Analog In"
{
    Frame label="Industrial Dual Analog In" {
        Text item=channel0
        Text item=channel1
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
tinkerforge:relay0.uid=<your_uid>
tinkerforge:relay0.type=quad_relay
tinkerforge:relay0.subid=relay0

tinkerforge:relay1.uid=<your_uid>
tinkerforge:relay1.type=quad_relay
tinkerforge:relay1.subid=relay1

tinkerforge:relay2.uid=<your_uid>
tinkerforge:relay2.type=quad_relay
tinkerforge:relay2.subid=relay2

tinkerforge:relay3.uid=<your_uid>
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

If you set the property $debouncePeriod to 100, you will get the interrupt maximal every 100ms. This is necessary if something that bounces is connected to the IO-16 Bricklet, such as a button.

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
tinkerforge:io16.uid=<your_uid>
tinkerforge:io16.type=bricklet_io16
tinkerforge:io16.debouncePeriod=100

tinkerforge:io16ina0.uid=<your_uid>
tinkerforge:io16ina0.subid=ina0
tinkerforge:io16ina0.type=iosensor
tinkerforge:io16ina0.pullUpResistorEnabled=true

tinkerforge:io16ina1.uid=<your_uid>
tinkerforge:io16ina1.subid=ina1
tinkerforge:io16ina1.type=iosensor
tinkerforge:io16ina1.pullUpResistorEnabled=true

tinkerforge:io16outa2.uid=<your_uid>
tinkerforge:io16outa2.subid=outa2
tinkerforge:io16outa2.type=io_actuator

tinkerforge:io16outa3.uid=<your_uid>
tinkerforge:io16outa3.subid=outa3
tinkerforge:io16outa3.type=io_actuator

tinkerforge:io16ina4.uid=<your_uid>
tinkerforge:io16ina4.subid=ina4
tinkerforge:io16ina4.type=iosensor
tinkerforge:io16ina4.pullUpResistorEnabled=true

tinkerforge:io16ina5.uid=<your_uid>
tinkerforge:io16ina5.subid=ina5
tinkerforge:io16ina5.type=iosensor
tinkerforge:io16ina5.pullUpResistorEnabled=true

tinkerforge:io16ina6.uid=<your_uid>
tinkerforge:io16ina6.subid=ina6
tinkerforge:io16ina6.type=iosensor
tinkerforge:io16ina6.pullUpResistorEnabled=true

tinkerforge:io16ina7.uid=<your_uid>
tinkerforge:io16ina7.subid=ina7
tinkerforge:io16ina7.type=iosensor
tinkerforge:io16ina7.pullUpResistorEnabled=true

#### port b

tinkerforge:io16outb0.uid=<your_uid>
tinkerforge:io16outb0.subid=outb0
tinkerforge:io16outb0.type=io_actuator

tinkerforge:io16inb1.uid=<your_uid>
tinkerforge:io16inb1.subid=inb1
tinkerforge:io16inb1.type=iosensor
tinkerforge:io16inb1.pullUpResistorEnabled=true

tinkerforge:io16inb2.uid=<your_uid>
tinkerforge:io16inb2.subid=inb2
tinkerforge:io16inb2.type=iosensor
tinkerforge:io16inb2.pullUpResistorEnabled=true

tinkerforge:io16inb3.uid=<your_uid>
tinkerforge:io16inb3.subid=inb3
tinkerforge:io16inb3.type=iosensor
tinkerforge:io16inb3.pullUpResistorEnabled=true

tinkerforge:io16inb4.uid=<your_uid>
tinkerforge:io16inb4.subid=inb4
tinkerforge:io16inb4.type=iosensor
tinkerforge:io16inb4.pullUpResistorEnabled=true

tinkerforge:io16inb5.uid=<your_uid>
tinkerforge:io16inb5.subid=inb5
tinkerforge:io16inb5.type=iosensor
tinkerforge:io16inb5.pullUpResistorEnabled=true

tinkerforge:io16inb6.uid=<your_uid>
tinkerforge:io16inb6.subid=inb6
tinkerforge:io16inb6.type=iosensor
tinkerforge:io16inb6.pullUpResistorEnabled=true

tinkerforge:io16inb7.uid=<your_uid>
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

Switch outb0		"outb0" {tinkerforge="uid=<your_uid>, subid=outb0"}
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
tinkerforge:joystick.uid=<your_uid>
tinkerforge:joystick.type=bricklet_joystick
tinkerforge:joystick.callbackPeriod=1000

tinkerforge:joystickbutton.uid=<your_uid>
tinkerforge:joystickbutton.subid=joystick_button
tinkerforge:joystickbutton.type=joystick_button
tinkerforge:joystickbutton.tactile=True

tinkerforge:yposition.uid=<your_uid>
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

### Laser Range Finder Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Laser_Range_Finder.html)

#### Binding properties:

The laser will be enabled by default on system start. This can be changed by 
setting enableLaserOnStartup to false on the bricklet_laser_range_finder type in openhab.cfg.
If the laser is already enabled it will never be disabled on openHAB startup.

Moving average is a calculation to analyze data points by creating series of averages of different subsets of the full data set.

```
tinkerforge:lrf.uid=<your_uid>
tinkerforge:lrf.type=bricklet_laser_range_finder
tinkerforge:lrf.enableLaserOnStartup=false
```

Properties $distanceAverageLength and $velocityAverageLength set the length of a moving averaging for the distance and velocity. Setting the property to 0 will turn the averaging completely off. With less averaging, there is more noise on the data. The range for the averaging is 0-30, default value is 10.

Property $mode sets the mode for measurements, five modes are available, one mode for distance measurements and four modes for velocity measurements with different ranges and resolutions. 

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_laser_range_finder |
| distanceAverageLength | sets the length of a moving averaging for the distance, default=10 | 0-30 |
| velocityAverageLength | sets the length of a moving averaging for the velocity, default=10 | 0-30 |
| mode | sets the mode for measurements, default=0 | 0: Distance is measured with resolution 1.0 cm and range 0-400 cm |
| mode | sets the mode for measurements, default=0 | 1: Velocity is measured with resolution 0.1 m/s and range is 0-12.7 m/s |
| mode | sets the mode for measurements, default=0 | 2: Velocity is measured with resolution 0.25 m/s and range is 0-31.75 m/s |
| mode | sets the mode for measurements, default=0 | 3: Velocity is measured with resolution 0.5 m/s and range is 0-63.5 m/s |
| mode | sets the mode for measurements, default=0 | 4: Velocity is measured with resolution 1.0 m/s and range is 0-127 m/s |
| enableLaserOnStartup | enables or disables laser on startup, default=true | true, false |

##### Laser Range Finder sub devices:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | distance, velocity, laser |
| type | openHAB type name | laser_range_finder_distance, laser_range_finder_velocity, laser_range_finder_laser |
| callbackPeriod | | see "Callback and Threshold" |
| threshold | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:lrf.uid=<your_uid>
tinkerforge:lrf.type=bricklet_laser_range_finder
tinkerforge:lrf.distanceAverageLength=10
tinkerforge:lrf.velocityAverageLength=10
tinkerforge:lrf.mode=1
tinkerforge:lrf.enableLaserOnStartup=True

tinkerforge:distance.uid=<your_uid>
tinkerforge:distance.subid=distance
tinkerforge:distance.type=laser_range_finder_distance
tinkerforge:distance.callbackPeriod=10
tinkerforge:distance.threshold=0

tinkerforge:velocity.uid=<your_uid>
tinkerforge:velocity.subid=velocity
tinkerforge:velocity.type=laser_range_finder_velocity
tinkerforge:velocity.callbackPeriod=10
tinkerforge:velocity.threshold=0

tinkerforge:laser.uid=<your_uid>
tinkerforge:laser.subid=laser
tinkerforge:laser.type=laser_range_finder_laser

```

##### Items file entry (e.g. tinkerforge.items):
```
Number distance "Distance [%.0f]" {tinkerforge="uid=<your_uid>, subid=distance"}
Number velocity "Velocity [%.4f]" {tinkerforge="uid=<your_uid>, subid=velocity"}
Switch laser "Enable Laser" {tinkerforge="uid=<your_uid>, subid=laser"}
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap led label="Laser Range Finder"
{
    Frame label="Laser Range Finder" {
        Text item=distance
        Text item=velocity
        Switch item=laser
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
Supported chip types are WS2801, WS2811 and WS2812 (also called "NeoPixel").
An entry in openhab.cfg is *mandatory*. $type, $frameduration, $chiptype, $clockfrequency and $colorMapping have to be set. The available configuration variables depend on the chip type of the LED strip.  
All LEDs can be switched independently. A subdevice $ledgroup can be set to group LED's together.  
The colormapping of the LED chip types are not standardized, therefore the sequence of the letters "rgb" can be changed individually to match the the color of your LED Stripswitch. 

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_ledstrip |
| framduration | sets the frame duration in ms, default: 100ms (10 fps) |  |
| chiptype | sets the strip chip type |  ws2801, ws2811, ws2812 |
| clockfrequency | sets the frequency of the clock in Hz | 10000Hz (10kHz) up to 2000000Hz (2MHz) |
| colorMapping | sets the colormapping | rbg or any other letter sequence of "r", "g" and "b"|
| subDevices | configures a group of led | f.e. ledgroup1 ledgroup2 |

##### LED Strip sub device (optional):

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | ledgroup1 |
| leds | configures a group of led's | depends on the number of led's used |

##### openhab.cfg:
```
tinkerforge:ledstrip.uid=<your_uid>
tinkerforge:ledstrip.type=bricklet_ledstrip
tinkerforge:ledstrip.frameduration=100
tinkerforge:ledstrip.chiptype=ws2801
tinkerforge:ledstrip.clockfrequency=1000000
tinkerforge:ledstrip.colorMapping=rbg
tinkerforge:ledstrip.subDevices=ledgroup1 ledgroup2

tinkerforge:ledgroup1.uid=<your_uid>
tinkerforge:ledgroup1.subid=ledgroup1
tinkerforge:ledgroup1.type=ledgroup
tinkerforge:ledgroup1.leds=0|1-6

tinkerforge:ledgroup2.uid=<your_uid>
tinkerforge:ledgroup2.subid=ledgroup2
tinkerforge:ledgroup2.type=ledgroup
tinkerforge:ledgroup2.leds=0|7-14

```

##### Items file entry (e.g. tinkerforge.items):
```
Color  tfled1   <slider> {tinkerforge="uid=<your_uid>, subid=ledgroup1"}
Color  tfled2   <slider> {tinkerforge="uid=<your_uid>, subid=ledgroup2"}
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

You can expect values from 0 - 100 %.
The default callback period is 10 millis, you can change this within openhab.cfg.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_linear_poti |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:linearpoti.uid=<your_uid>
tinkerforge:linearpoti.type=bricklet_linear_poti
tinkerforge:linearpoti.callbackPeriod=1000
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Poti   "Poti [%.0f]" { tinkerforge="uid=<your_uid>"}
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

Returns the currently measured weight in grams. An LED can be turned on to inidicate that a weight measurement is in range.

Moving average is a calculation to analyze data points by creating series of averages of different subsets of the full data set.

##### TinkerForge Action
The openHAB action [TinkerforgeAction](#tinkerforge-actions) comes up with the action tfLoadCellTare.
tfLoadCellTare(String uid) sets tare on the load cell bricklet with the given uid.

Example:
```
rule "Tare"
   when 
           Item Tare changed to ON
   then
           postUpdate(TareValue, Weight.state)
           tfLoadCellTare("<your_uid>")
end
```    

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_loadcell |


##### Load Cell sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | weight, led |
| type | openHAB type name | loadcell_weight, loadcell_led |
| movingAverage | sets the value for moving average, default=4 | 1-40 |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:loadcell.uid=<your_uid>
tinkerforge:loadcell.type=bricklet_loadcell

tinkerforge:weight.uid=<your_uid>
tinkerforge:weight.subid=weight
tinkerforge:weight.type=loadcell_weight
tinkerforge:weight.callbackPeriod=100
tinkerforge:weight.movingAverage=4

tinkerforge:led.uid=<your_uid>
tinkerforge:led.subid=led
tinkerforge:led.type=loadcell_led
```

##### Items file entry (e.g. tinkerforge.items):
```
Number Weight "Weight [%.0f]" { tinkerforge="uid=<your_uid>, subid=weight"}
Switch Led "Led"  {tinkerforge="uid=<your_uid>, subid=led"}
Switch Tare "Tare"
Number TareValue "Tare Value [%.0f]"
```

##### Sitemap file entry (e.g tinkerforge.sitemap):
```
sitemap tf label="Load Cell"
{
  Frame {
	Text item=Weight
	Switch item=Led
	Switch item=Tare
	Text item=TareValue
	}
}
```

##### Rules (e.g tinkerforge.rules):
```
import org.openhab.core.library.types.*

rule "Tare"
        when 
                Item Tare changed to ON
        then
                postUpdate(TareValue, Weight.state)
                tfLoadCellTare("<your_uid>")
end
```
---

### Motion Detector Bricklet

Technical description see [Tinkerforge Website](http://www.tinkerforge.com/en/doc/Hardware/Bricklets/Motion_Detector.html)

#### Binding properties:

Senses movement of people and animals with a detection range of 3m to 7m and a sensing angle of 100°.

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
| sensitivity | sets the electrodes sensitivity, default=181 | 5-201 |
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

##### openhab.cfg for electrodes:

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

##### openhab.cfg for proximity:

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

Moving average is a calculation to analyze data points by creating series of averages of different subsets of the full data set.

##### openhab.cfg:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_moisture |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |
| movingAverage | sets the value for moving average, default=100 | 0-100 |

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

Wire mode of the sensor has to be set. Possible values are 2, 3 and 4 which correspond to 2-, 3- and 4-wire sensors. The value has to match the jumper configuration on the Bricklet.

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_ptc |
| wiremode | sets the wire mode of the sensor, default=2 | 2, 3, 4 |

##### PTC sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | same as bricklet |
| subid | openHAB subid of the device | ptc_temperature |
| type | openHAB type name | ptc_temperature |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:brickletptc.uid=<your_uid>
tinkerforge:brickletptc.type=bricklet_ptc
tinkerforge:brickletptc.wiremode=2

tinkerforge:ptctemperature.uid=<your_uid>
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
| typeADevices | sub device names of type A devices | choose a reasonable string, e.g. "kitchen" or "floor" |
| typeBDevices | sub device names of type B devices  | choose a reasonable string |
| typeCDevices | sub device names of type C devices  |choose a reasonable string |

```
tinkerforge:rs1.uid=<your_uid>
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
| receiverCode | the receiver code of the switching device | e.g. 8 |
| repeats | the number of times the code is send | e.g. 5 |

```
tinkerforge:rs_living_room.uid=<your_uid>
tinkerforge:rs_living_room.subid=rslr1
tinkerforge:rs_living_room.type=remote_switch_a
tinkerforge:rs_living_room.houseCode=31
tinkerforge:rs_living_room.receiverCode=8

tinkerforge:rs_living_room2.uid=<your_uid>
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
tinkerforge:rs_kitchen.uid=<your_uid>
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
tinkerforge:rs_floor.uid=<your_uid>
tinkerforge:rs_floor.subid=floor
tinkerforge:rs_floor.type=remote_switch_c
tinkerforge:rs_floor.systemCode=A
tinkerforge:rs_floor.deviceCode=8
```

##### Items file entry (e.g. tinkerforge.items):
```
Dimmer dimmb     "dimmb [%d %%]"      <slider>     {tinkerforge="uid=<your_uid>, subid=kitchen"}
Switch r0    "r0" <socket> (Lights)      {tinkerforge="uid=<your_uid>, subid=rslr1"}
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
  Frame label="Remote" {
			Switch item=r0
			Switch item=r1
			Switch item=rb
			Slider item=dimmb switchSupport
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

##### TinkerForge Action
The openHAB action [TinkerforgeAction](#tinkerforge-actions) comes up with the action tfRotaryEncoderClear.
tfRotaryEncoderClear(String uid) clears the rotary encoder counter with the given uid.

Example:
```
rule "Clear"
   when Item Clear changed
then 
   tfRotaryEncoderClear("<your_uid>")
end
```    

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
tinkerforge:encoder.uid=<your_uid>
tinkerforge:encoder.subid=encoder
tinkerforge:encoder.type=rotary_encoder
tinkerforge:encoder.callbackPeriod=1

tinkerforge:button.uid=<your_uid>
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
http://www.tinkerforge.com/en/doc/Software/Bricklets/Temperature_Bricklet_Java.html#BrickletTemperature::setI2CMode__short-

##### Bricklet:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| type | openHAB type name | bricklet_temperature |
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
```
tinkerforge:temperature.uid=<your_uid>
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
| type | openHAB type name | bricklet_temperatureIR |

##### Object temperature sub device:

| property | descripition | values |
|----------|--------------|--------|
| uid | tinkerforge uid | get value from brickv |
| subid | openHAB subid of the device | object_temperature |
| type | openHAB type name | object_temperature |
| emissivity |  emissivity that is used to calculate the surface temperature | a factor of 65535 e.g. 6553|
| threshold | | see "Callback and Threshold" |
| callbackPeriod | | see "Callback and Threshold" |

##### openhab.cfg:
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

##### openhab.cfg:
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
Contact tiltContact  "tilt [MAP(en.map):%s]" { tinkerforge="uid=<your_uid>" }
Number tiltSensor    "tilt [MAP(en.map):%s]"  { tinkerforge="uid=<your_uid>" }
Switch tiltSwitch    "tilt" { tinkerforge="uid=<your_uid>" }
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

##### openhab.cfg:
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

##### openhab.cfg:
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

##### openhab.cfg:
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
tinkerforge:lcdbutton2.uid=<your_uid>
tinkerforge:lcdbutton2.subid=button2
tinkerforge:lcdbutton2.type=lcd_button
tinkerforge:lcdbutton2.tactile=True

tinkerforge:lcdbutton3.uid=<your_uid>
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

## TinkerForge Actions

The TinkerForge Action plugin provides direct interaction with some of the TinkerForge devices (since 1.7.0). The action depends on the TinkerForge binding. In order to use these actions you must install the TinkerForge Action bundle and the [TinkerForge Binding](https://github.com/openhab/openhab/wiki/Tinkerforge-Binding) and add at least a hosts configuration value for the binding in openhab.cfg.
These action functions are available:

1. `tfClearLCD(String uid)`

    Clears the display of the LCD with the given uid.

    Example:
    ```
    rule "clear lcd"
        when
            Item ClearLCD received command ON
        then
           tfClearLCD("<your_uid>")
    end
    ```
2. `tfServoSetposition(String uid, String num, String position, String velocity, String acceleration)`

    Sets the position of a TinkerForge servo with the uid $uid and servo number to the position $position using the speed $speed and acceleration $acceleration.

    Example:
    ```
    rule "move servo"
      when
          Item MoveServo received command ON
      then
         tfServoSetposition("<your_uid>", "servo0", "0", "65535", "65535")
         Thread::sleep(1000)
         tfServoSetposition("<your_uid>", "servo0", "-9000", "65535", "65535")
         Thread::sleep(1000)
        tfServoSetposition("<your_uid>", "servo0", "9000", "65535", "65535")
    end
    ```
3. `tfDCMotorSetspeed(String uid, String speed, String acceleration, String drivemode)`

    Sets the speed of a TinkerForge DC motor with the given uid to $speed using the acceleration $acceleration and the drivemode $drivemode.
    * speed: value between -32767 - 32767
    * drivemode is either "break" or "coast"

    Example:
    ```
    rule "move motor"
      when
          Item DCMOVE received command ON
      then
         var String acceleration = "10000"
         var String speed = "15000"
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
         Thread::sleep(1000)
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
         Thread::sleep(1000)
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
         Thread::sleep(1000)
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
    end
    ```
4. `tfDCMotorSetspeed(String uid, Short speed, Integer acceleration, String drivemode)`

    Sets the speed of a TinkerForge DC motor with the given uid to $speed using the acceleration $acceleration and the drivemode $drivemode.
    * speed: value between -32767 - 32767
    * drivemode is either "break" or "coast"

    Example:
    ```
    rule "move motor"
      when
          Item DCMOVE received command ON
      then
         var Integer acceleration = 10000
         var Short speed = 15000
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
         Thread::sleep(1000)
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
         Thread::sleep(1000)
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
         Thread::sleep(1000)
         tfDCMotorSetspeed("<your_uid>", speed, acceleration, "break")
    end
    ```
5. `tfRotaryEncoderClear(String uid)`

	Clears the rotary encoder counter with the given uid.

    Example:
    ```
    rule "Clear"
        when Item Clear changed
    then 
	   tfRotaryEncoderClear("kHv")
    end
    ```
 
6. `tfLoadCellTare(String uid)`
 	
    Sets tare on the load cell bricklet with the given uid.
    
    Example:
    ```
    rule "Tare"
        when 
                Item Tare changed to ON
        then
                postUpdate(TareValue, Weight.state)
                tfLoadCellTare("<your_uid>")
    end
    ```    
---

## Upgrading
### Upgrading from 1.3
- LCDBacklight now is a sub device of [LCD 20×4 Bricklet](#lcd-20x4-display-bricklet) (items file has to be changed)
- [LCD20×4Button](#lcd-20x4-display-bricklet) now posts an update not a command anymore (rules has to be changed)
- [IndustrialQuadRelay](#industrial-quad-relay-bricklet) sub id numbering now starts from zero (items file has to be changed)

---

### Upgrading from 1.4
- Threshold values now have the same unit as the sensor value (incompatible change, you may have to update your openhab.cfg).
   - Background:
     Some getters for sensor values of the TinkerForge API return higher precision values by using short values with fractions of the common units, e.g. the Temperature Bricklet returns hundredths of a celsius degree. The binding converts these values to common units using a BigDecimal. Until now the threshold values were applied to the sensor value before this conversion. Because of that the threshold values had to be given as the appropriate fraction. With the drawback that the openHAB users need some knowledge about the behavior of the TinkerForge API. Now the threshold is applied after converting the original values. Therefore the units used for the sensor values and the threshold values are equal.
    - [Humidity Bricklet](#humidity-bricklet)
        - calculate new threshold values from values of your current configuration: divide by 10
        - unity: relative humidity in percent
    - [Distance IR Bricklet](#distance-ir-bricklet)
       - calculate new threshold values from values of your current configuration: nothing changed
       -  unity: millimeter
    - [Temperature Bricklet](#temperature-bricklet)
       - calculate new threshold values from values of your current configuration: divide by 100
       - unity: degree Celsius
    - [Barometer Bricklet](#barometer-bricklet)
       - calculate new threshold values from values of your current configuration: divide by 1000
       - unity: mbar
    - [Ambient Light Bricklet](#ambient-light-bricklet-v2)
       - calculate new threshold values from values of your current configuration: divide 10
       - unity: Lux

---

### Upgrading from 1.7.0
- New Devices
 - [Joystick Bricklet](#joystick-bricklet)
 - [Linear Poti Bricklet](#linear-poti-bricklet)
 - [Dual Button Bricklet](#dual-button-bricklet)
 - [PTC Bricklet](#ptc-bricklet)
 - [Industrial Dual 0-20mA Bricklet](#industrial-dual-0-20ma-bricklet)
 - [Solid State Relay Bricklet](#solid-state-relay-bricklet)

- New Features
 - [Tinkerforge Action](#tinkerforge-actions) Addon
 - [Brick DC](#dc-brick) fully supported
 - [Brick Servo](#servo-brick) fully supported
 - Authentication support for brickd
 - Tactile feature for [Dualbutton](#dual-button-bricklet), [LCD Buttons](#lcd-20x4-display-bricklet), [Joystick Button](#joystick-bricklet).
 - [LED Strip](#led-strip-bricklet): sub devices and switching capabilities, configurable Frameduration, ChipType and Clockfrequency
 - [Remote Switch dimmer](#remote-switch-bricklet)

- Bugfixes
 - Fix for configuration handling of device aliases

- Other changes
 - Updated Tinkerforge API to 2.1.4
 - [Example configurations](https://github.com/theoweiss/openhab-tinkerforge-configuration-examples) available on github

 ---

### Upgrading from 1.7.1
 - New Devices
  - [Accelerometer Bricklet](#accelerometer-bricklet)
  - [Ambient Light Bricklet 2.0](#ambient-light-bricklet-v2)
  - [Analog In Bricklet](#analog-in-bricklet)
  - [Analog In Bricklet 2.0](#analog-in-bricklet-20)
  - [Color Bricklet](#color-bricklet)
  - [Dust Detector Bricklet](#dust-detector-bricklet)
  - [Hall Effect Bricklet](#hall-effect-bricklet)
  - [Industrial Dual Analog In Bricklet](#industrial-dual-analog-in-bricklet)
  - [Laser Range Finder Bricklet](#laser-range-finder-bricklet)
  - [Load Cell Bricklet](#load-cell-bricklet)
  - [Piezo Speaker Bricklet](#piezo-speaker-bricklet)
  - [Rotary Encoder Bricklet](#rotary-encoder-bricklet)
  - [Temperature IR Bricklet](#temperature-ir-bricklet)
  
### Upgrading from 1.8.0

more to come ...

---
