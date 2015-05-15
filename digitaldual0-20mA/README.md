Industial Dual 0-20mA Bricklet
==============================
 * two sub devices: sensor0 and sensor1
 * device type bricklet: bricklet_industrialdual020ma
 * device type sensors: industrial020ma_sensor
 * sensor values are reported as milli ampere

openhab.cfg configuration options for the Bricklet
==================================================
sampleRate: possible values 0, 1, 2, 3. Setting the sample rate is optional 
it defaults to 3 (4 samples per second).
 * 0 means: 240 samples per second
 * 1 means: 60 samples per second
 * 2 means: 15 samples per second
 * 3 means: 4 samples per second

openhab.cfg configuration options for the sensors
=================================================
callbackPeriod: Setting the callback period is optional, the default is 1000 milli seconds.
threshold: 

