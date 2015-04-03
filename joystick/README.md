Joystick Bricklet
=================
There are three sub devices: joystick_xposition, joystick_yposition and joystick_button.
Callback period for xy position defaults to 10 milli seconds. CallbackPeriod must be configured
on bricklet_joystick device, because x and y position can not have
different callback periods.

Button
=======
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
