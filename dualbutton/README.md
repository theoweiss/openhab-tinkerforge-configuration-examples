Dual Button Bricklet
====================
The Dual Button Bricklet has four sub devices: two leds and two buttons. 
The subids are: 
 * dualbutton_leftled
 * dualbutton_rightled
 * dualbutton_leftbutton
 * dualbutton_rightbutton

Leds
====
There are two operating modes for the leds: with the autotoggle=True the leds are
automatically toggled whenever the corresponding button is pressed. With the autotoggle=False
mode the leds are fully controlled with openHAB UIs or rules. The default autotoggle mode is 
autotoggle=False. The autotoggle mode can be configured using openhab.cfg.

Buttons
=======
There are also two operating modes for the buttons. The buttons can behave like a switch or 
like a tactile switch.  
* Switch mode
The switch mode operates like this: pressing the button toggles the
switch state, if state was ON it goes to OFF and vice versa. Releasing the button doesn't 
change anything, only the next button press will change the state.
* Tactile switch mode
Pressing the button changes the switch state to ON and releasing the button changes the
state back to OFF again.

