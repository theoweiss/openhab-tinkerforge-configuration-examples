Laser Range Finder Bricklet
===========================
The laser will be enabled by default on system start. This can be changed by 
setting enableLaserOnStartup to false on the bricklet_laser_range_finder type in openhab.cfg.
If the laser is already enabled it will never be disabled on openHAB startup.

```
	tinkerforge:lrf.uid=vc6
	tinkerforge:lrf.type=bricklet_laser_range_finder
	tinkerforge:lrf.enableLaserOnStartup=false
```
