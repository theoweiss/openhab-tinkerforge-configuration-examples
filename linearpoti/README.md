Linear Poti Bricklet
====================
There is on device called bricklet_linear_poti. You can expect values from 0 - 100.
The default callback period is 10 millis, you can change this with openhab.cfg.


Example
========
openhab.cfg
===========
```
tinkerforge:linearpoti.uid=egW
tinkerforge:linearpoti.type=bricklet_linear_poti
tinkerforge:linearpoti.callbackPeriod=1000
```
Item
====
```
Number Poti              "Poti [%.0f]" { tinkerforge="uid=egW"}
```
Sitemap
=======
```
sitemap tflabel="Linear Poti"
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
