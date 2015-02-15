Joystick Bricklet
=================
There are three sub devices: joystick_xposition, joystick_yposition and joystick_button.
Callback period for xy position defaults to 10 milli seconds. CallbackPeriod must be configured
on bricklet_joystick device, because x and y position can not have
different callback periods.

The joystick_button has a value of HighLowValue.LOW when pressed, else the value is HighLowValue.HIGH.

Example
========
openhab.cfg
===========
```
tinkerforge:joystick.uid=aVp
tinkerforge:joystick.type=bricklet_joystick
tinkerforge:joystick.callbackPeriod=1000
```
Item
====
```
Number XPostion              "XPostion [%.0f]" { tinkerforge="uid=aVp, subid=joystick_xposition"}
Number YPostion              "YPostion [%.0f]" { tinkerforge="uid=aVp, subid=joystick_yposition"}
Contact JoystickButton       "Button" { tinkerforge="uid=aVp, subid=joystick_button"}
```
Sitemap
=======
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
Rules
=====
```
```
