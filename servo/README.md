# Whats new?
Support for Dimmer, Rollershuter and Number items. Besides that the speed
can be set using a percent value.

Number items will show the current position. 

# New item configuration options
* velocity: the velocity used to reach the new position
* max: the maximum position (Dimmer, Rollershutter)
* min: the minimum position (Dimmer, Rollershutter)
* step: the step value for increasing decreasing position (Dimmer)
* leftposition: the target position to reach when the left rollershutter controller is pressed or command "DOWN" was send
* rightposition: the target position to reach when the right rollershutter controller is pressed or command "UP" was send
* acceleration: the acceleration

# TinkerForge Action
The new openHAB action TinkerForgeAction comes up with the action tfServoSetposition.
tfServoSetposition(uid, num, position, velocity, acceleration) can be used to control the servo.
## Example
```
tfServoSetposition("6Crt5W", "servo0", "-9000", "65535", "65535")
```