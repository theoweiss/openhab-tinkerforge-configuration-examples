Download
========
https://bintray.com/artifact/download/theoweiss/generic/org.openhab.binding.tinkerforge-1.7.0-SNAPSHOT.jar
The binding will work with 1.6.x versions of openHAB.

Incompatible changes
====================
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


Whats new?
==========
Support for Dimmer, Rollershuter and Number items. Besides that the speed
can be set using a percent value.

The number items show the current velocity. The values are reported using the VelocityListener. 
"callbackPeriod" and "threshold" for the listener can be configured in openhab.cfg. There is more
documentation about callback listeners at the official openHAB TinkerForgeBindig wiki page.

* callbackPeriod: milliseconds
* threshold: numeric value

New item configuration options
==============================
* speed: the target speed (Switch)
* max: the maximum speed (Dimmer, Rollershutter)
* min: the minimum speed (Dimmer, Rollershutter)
* step: the step value for increasing decreasing speed (Dimmer)
* leftspeed: the speed when the left rollershutter controller is pressed or command "DOWN" was send
* rightspeed: the speed when the right rollershutter controller is pressed or command "UP" was send
* acceleration: acceleration overrides value from openhab.cfg
* drivemode: drivemode  overrides value from openhab.cfg

Example
========
openhab.cfg
===========
```
tinkerforge:dc_garage.uid=62Zduj
tinkerforge:dc_garage.type=brick_dc
tinkerforge:dc_garage.pwmFrequency=15000
tinkerforge:dc_garage.driveMode=break
tinkerforge:dc_garage.acceleration=10000
tinkerforge:dc_garage.callbackPeriod=100
```
Item
====
```
Dimmer  DCDIMMER  "Dimmer" {tinkerforge="uid=62Zduj, max=20000, min=-15000, acceleration=10000, drivemode=brake, step=2500"}
Dimmer  DIMMERPERCENT  "Dimmerpercent" {tinkerforge="uid=62Zduj, max=20000, min=0, acceleration=10000, drivemode=brake, step=2500"}
Rollershutter TF_ROLLER "Roller" {tinkerforge="uid=62Zduj, leftspeed=10000, rightspeed=-10000, acceleration=10000, drivemode=brake"}
Switch DCSWITCH "DC Switch" {tinkerforge="uid=62Zduj, speed=14000"}
Number DCSPEED "DC Speed [%.0f]"  {tinkerforge="uid=62Zduj, max=20000, min=-15000, step=1000, leftspeed=10000, rightspeed=-10000, acceleration=10000, drivemode=brake"}
Dimmer  RULEDIMMER  "RuleDimmer" 
```
Sitemap
=======
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
Rules
=====
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
```
