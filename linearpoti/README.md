Linear Poti Bricklet
====================
There is on device called bricklet_linear_poti. You can expect values from 0 - 100.
The default callback period is 10 millis, you can change this with openhab.cfg.


Example
========
openhab.cfg
===========
```
tinkerforge:joystick.uid=egW
tinkerforge:joystick.type=bricklet_linear_poti
tinkerforge:joystick.callbackPeriod=1000
```
Item
====
```
Number Poti              "Poti [%.0f]" { tinkerforge="uid=egW"}
```
Sitemap
=======
```
sitemap tf_weather label="Linear Poti"
{
  Frame {
	Text item=Poti
	}
}
```
Rules
=====
```
```
