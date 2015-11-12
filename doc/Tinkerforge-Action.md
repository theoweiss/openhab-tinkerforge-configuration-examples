## Actions available in Scripts and Rules

Actions are predefined Java methods that are automatically statically imported and can be used within [[Scripts]] and [[Rules]] to execute openHAB specific operations.

Since openHAB 1.3.0, not all actions are part of the core runtime distribution anymore, but it is possible to easily implement add new actions to your runtime (see the [developer section](How-To-Implement-An-Action) for details).

## Table of Contents

* [Core Actions](#core-actions)
  * [Event bus](#event-bus-related-actions)
  * [Audio](#audio-actions)
  * [Logging](#logging-actions)
  * [HTTP](#http-actions)
  * [Other](#other-actions)
* [Add-on Actions](#add-on-actions)
  * [Astro](#astro-actions)
  * [[DSC Alarm|DSC-Alarm-Action]]
  * [Ecobee](#ecobee-actions)
  * [[Harmony Hub|Logitech-Harmony-Hub-Binding#actions]]
  * [[Homematic|Homematic-Binding#homematic-action]]
  * [Mail](#mail-actions)
  * [[MiOS (Vera)|MiOS-Action]]
  * [MQTT](#mqtt-action)
  * [my.openHAB](https://my.openhab.org/docs/notifications)
  * [NotifyMyAndroid](#notifymyandroid-actions)
  * [OpenWebIf](#openwebif-action)
  * [Prowl](#prowl-actions)
  * [Pushover](#pushover-actions)
  * [Squeezebox](#squeezebox-action)
  * [TinkerForge](#tinkerforge-actions)
  * [Twitter](#twitter-actions)
  * [Weather](#weather-actions)
  * [XBMC](#xbmc-actions)
  * [XMPP](#xmpp-actions)
  * [[xPL|xPL-Binding#xpl-action]]

## Core Actions

Here is the list of available actions in the core runtime:

### Event bus related actions
- `sendCommand(String itemName, String commandString)`: Sends the given command for the specified item to the event bus
- `postUpdate(String itemName, String stateString)`: Posts the given status update for the specified item to the event bus
- `Map<Item, State> storeStates(Item... items)`: Stores the current state of a list of items in a map which can be assigned to a variable. Group items are not themselves put into the map, but instead all their members.
- `restoreStates(Map<Item, State> statesMap)`: Restores item states from a map. If the saved state can be interpreted as a command, a command is sent for the item (and the physical device can send a status update if occurred). If it is no valid command, the item state is directly updated to the saved value.

[Table of Contents](#table-of-contents)

### Audio actions
- `setMasterVolume(float volume)`: Sets the volume of the host machine (volume in range 0-1)
- `increaseMasterVolume(float percent)`: Increases the volume by x percent
- `decreaseMasterVolume(float percent)`: Decreases the volume by x percent
- `float getMasterVolume()`: Returns the volume with the range 0-1
- `playSound(String filename)`: Plays the given file (must be mp3 or wav and be located in `${openhab.home}/sounds`)
- `playStream(String url)`: Plays the audio stream from the given url.
- `say(String text)`: Says the given text through Text-to-speech
- `say(String text, String voice)`: Text-to-speech with a given voice (depends on the TTS engine or voices installed in the OS)
- `say(String text, String voice, String device)`: Text-to-speech with a given voice to the given output device (only supported on Mac OS). Available voices command: `say -v ?`. Available devices command: `say -a ?`

[Table of Contents](#table-of-contents)

### Logging actions
- `logDebug(String loggerName, String logText, Object[args))`: Logs `logText` on level `DEBUG` using the openhab Logback configuration
- `logInfo(String loggerName, String logText, Object[](]) args))`: Logs `logText` on level `INFO` using the openhab Logback configuration
- `logWarn(String loggerName, String logText, Object[args))`: Logs `logText` on level `WARN` using the openhab Logback configuration
- `logError(String loggerName, String logText, Object[](]) args))`: Logs `logText` on level `ERROR` using the openhab Logback configuration

  Note: Opened and immediately closed curly braces (`{}`) in `logText` are placeholders for `args[0]`, `args[1]`, etc.

  Note2: Remember that if the name of your logger is "kitchen" you have to define it in logback.xml as `<logger name="org.openhab.model.script.kitchen" level="DEBUG"/>`

Example:
- `logDebug("Garage", "GarageDoorStateChanged Starting. Garage_Door_Open.state: [{}]. Garage_Door_Closed.state: [{}].", Garage_Door_Open.state, Garage_Door_Closed.state);`

 - Outputs `"GarageDoorStateChanged Starting. Garage_Door_Open.state: [true]. Garage_Door_Closed.state: [false]."` to a logger called "Garage". In this example, Garage_Door_Open and Garage_Door_Closed are String items with values of `true` and `false` respectively.

[Table of Contents](#table-of-contents)

### HTTP actions
- `sendHttpGetRequest(String url)`: Send out a GET-HTTP request.
- `sendHttpPutRequest(String url)`: Send out a PUT-HTTP request.
- `sendHttpPutRequest(String url, String contentType, String content)`: Send out a PUT-HTTP request, with parameters.
- `sendHttpPostRequest(String url)`: Send out a POST-HTTP request.
- `sendHttpPostRequest(String url, String contentType, String content)`: Send out a POST-HTTP request, with parameters.
- `sendHttpDeleteRequest(String url)`: Send out a DELETE-HTTP request.

[Table of Contents](#table-of-contents)

### Other actions
- `callScript(String scriptName)`: Calls a script which must be located in the configurations/scripts folder
- `createTimer(AbstractInstant instant, Procedure procedure)`: Schedules a block of code (a closure) for future execution.
- `executeCommandLine(String commandLine)`: Executes a command on the command line
- `executeCommandLine(String commandLine, int timeout)`: Executes a command on the command line with a timeout

[Table of Contents](#table-of-contents)

## Add-on Actions

The following actions can be found in the "addons" download package. In order to install them to your runtime, simply copy the according jar file to the `${openhab.home}/addons` folder.
To make these actions available in the Designer as well, you need to copy the jar files into the `addons` folder of the Designer (note that you have to view the package content of the Designer in order to find the addons folder, if you are on Mac OS X). If the `addons` folder does not exist yet, simply create it.

### Astro Actions

With the Astro-Action you can calculate sunrise and sunset DataTime values in rules.  
**Important:** The action also requires the corresponding Astro-Binding!

Example:
```
import org.openhab.core.library.types.*
import java.util.Date

rule "Astro Action Example"
when
	...
then
	var Date current = now.toDate
	var double lat = xx.xxxxxx
	var double lon = xx.xxxxxx

	logInfo("sunRiseStart: ", new DateTimeType(getAstroSunriseStart(current, lat, lon)).toString)
	logInfo("sunRiseEnd: ", new DateTimeType(getAstroSunriseEnd(current, lat, lon)).toString)

	logInfo("sunSetStart: ", new DateTimeType(getAstroSunsetStart(current, lat, lon)).toString)
	logInfo("sunSetEnd: ", new DateTimeType(getAstroSunsetEnd(current, lat, lon)).toString)
end
```

[Table of Contents](#table-of-contents)

### Ecobee Actions

_Available as of openHAB 1.8_

The Ecobee Action bundle provides actions such as setting and clearing program holds, sending a text message to the thermostat's display, renaming a remote wireless sensor, and other functions that cannot be performed by setting object properties.  The Action bundle requires that the [[Ecobee Binding|Ecobee-Binding]] is also installed in your `addons` folder.  Read the [Ecobee function API documentation](https://www.ecobee.com/home/developer/api/documentation/v1/functions/using-functions.shtml) to be sure you know the rules for calling these actions.

Parameters in _italics_ are optional, in which case each unused parameter must be replaced with `null`.  

* ecobeeAcknowledge(Item item, String thermostatIdentifier, String ackRef, String ackType, _Boolean remindMeLater_) - Acknowledge an alert.
* ecobeeControlPlug(Item item, String plugName, String plugState, _Date startDateTime_, _Date endDateTime_, _String holdType_, _Integer holdHours_) - Control the on/off state of a plug by setting a hold on the plug.
* ecobeeCreateVacation(Item item, String name, DecimalType coolHoldTemp, DecimalType heatHoldTemp, _Date startDateTime_, _Date endDateTime_, _String fan_, _Integer fanMinOnTime_) - Create a vacation event on the thermostat.
* ecobeeDeleteVacation(Item item, String name) - Delete a vacation event from a thermostat.
* ecobeeResetPreferences(Item item) - Set all user configurable settings back to the factory default values.
* ecobeeResumeProgram(Item item, _Boolean resumeAll_) - Remove the currently running event providing the event is not a mandatory demand response event.
* ecobeeSendMessage(Item item, String text) - Send an alert message to the thermostat.
* ecobeeSetHold(Item item, _DecimalType coolHoldTemp_, _DecimalType heatHoldTemp_, _String holdClimateRef_, _Date startDateTime_, _Date endDateTime_, _String holdType_, _Integer holdHours_) - Set the thermostat into a hold with the specified temperature.
* ecobeeSetOccupied(Item item, Boolean occupied, _Date startDateTime_, _Date endDateTime_, _String holdType_, _Integer holdHours_) - Switches a (EMS model only) thermostat from occupied mode to unoccupied, or vice versa.
* ecobeeUpdateSensor(Item item, String name, String deviceId, String sensorId) - Update the name of an ecobee3 remote sensor.

[Table of Contents](#table-of-contents)

### Mail Actions

This add-on provides SMTP services. Please check openhab_default.cfg for required configuration settings. The `to` paremeter can contain a semicolon separated list of email addresses.
- `sendMail(String to, String subject, String message)`: Sends an email via SMTP.
- `sendMail(String to, String subject, String message, String attachmentUrl)`: Sends an email with attachment via SMTP.
- `sendMail(String to, String subject, String message, List<String> attachmentUrlList)`: Sends an email with one or more attachments via SMTP *(available as of openHAB 1.8)*.  Example:
```
import java.util.List
...
val List<String> attachmentUrlList = newArrayList(
  "http://some.web/site/snap.jpg&param=value",
  "http://192.168.1.100/data.txt",
  "file:///tmp/201601011031.jpg")
sendMail("you@email.net", "Test", "This is the message.", attachmentUrlList)
```

[Table of Contents](#table-of-contents)

### MQTT Action

_Available as of openHAB 1.8_

* `publish(String brokerName, String topic, String message)`: Publish the message to topic using specified MQTT broker.

[Table of Contents](#table-of-contents)

### NotifyMyAndroid Actions

Sends push messages to your Android devices. All configuration in openhab.cfg is optional
- `notifyMyAndroid(String event, String description)`: Send a message to a the pre configured api key (account) and use the configured or default values for the other parameters
- `notifyMyAndroid(String apiKey, String event, String description)`: Send a message to another api key than the configured or use this method if you have not configured a default api key
- `notifyMyAndroid(String apiKey, String event, String description, int priority, String url, boolean html)`: Send a message overwriting all configured parameters and using the specified values.

[Table of Contents](#table-of-contents)

### OpenWebIf Action

The openwebif action allows you to send a message to enigma2 based linux sat receivers (Dreambox, VU+, Clarke-Tech, ...) with installed OpenWebIf plugin.

Configure your sat receivers in openhab.cfg, you can specify multiple sat receivers identified by name:
```
openwebif:receiver.<name>.host=
openwebif:receiver.<name>.port=
openwebif:receiver.<name>.user=
openwebif:receiver.<name>.password=
openwebif:receiver.<name>.https=
```

Example:
```
openwebif:receiver.main.host=vusolo2
openwebif:receiver.main.port=81
openwebif:receiver.main.user=root
openwebif:receiver.main.password=xxxxx
openwebif:receiver.main.https=false
```

Now you can send a message to the configured receiver:
- `sendOpenWebIfNotification(NAME, MESSAGE, TYPE, TIMEOUT);`  

**NAME:** The name of the sat receiver configured in openhab.cfg  
**MESSAGE:** The message to send to the receiver  
**TYPE:** The message type (INFO, WARNING, ERROR)  
**TIMEOUT:** How long the text will stay on the screen in seconds  

Example:  
`sendOpenWebIfNotification("main", "Hello World!\n\nThis is a message sent from openHab!", "WARNING", 10);`

![](https://farm4.staticflickr.com/3882/15284270826_8cf0e637d8_z.jpg)

[Table of Contents](#table-of-contents)

### Prowl Actions

Prowl lets you use push notifications on iOS devices (please check openhab.cfg for required configuration settings):
- `pushNotification(String subject, String message)`: Pushes a Prowl Notification
- `pushNotification(String apikey, String subject, String message, int priority)`: Pushes a Prowl Notification with the given priority

[Table of Contents](#table-of-contents)

### Pushover Actions

The pushover action allows you to notify mobile devices of a message using the Pushover API web service.

The following are configuration items for your openhab.cfg file. None of the options are required as you can specify required configuration items in the action call, but you must at least provide an _API Token_, _User/Group Key_ and a _Message_ in some manner before a message can be pushed.

- `pushover:defaultTimeout` - The timeout for the communication with the Pushover service.
- `pushover:defaultToken` - Pushover API token to send to devices.
- `pushover:defaultUser` - Pushover User or Group key to send to devices.
- `pushover:defaultTitle` - Application title for the notification.
- `pushover:defaultPriority` - Priority of the notification. Default is 0.
- `pushover:defaultUrl` - A URL to send with the notification.
- `pushover:defaultUtlTitle` - Title of the URL to send with the notification.
- `pushover:defaultRetry` - When priority is 2, how often in seconds should messages be resent. [Added 1.6+]
- `pushover:defaultExpire` - When priority is 2, how long to continue resending messages until acknowledged.  [Added 1.6+]

The following are valid action calls that can be made when the plugin is loaded. For specific information on each item, see the [Pushover API](https://pushover.net/api).

- `pushover(String message)`
- `pushover(String message, String device)`
- `pushover(String message, int priority)`
- `pushover(String message, int priority, String url)` [Added 1.6+]
- `pushover(String message, int priority, String url, String urlTitle)` [Added 1.6+]
- `pushover(String message, int priority, String url, String urlTitle, String soundFile)` [Added 1.6+]
- `pushover(String message, String device, int priority)`
- `pushover(String message, String device, int priority, String url)` [Added 1.6+]
- `pushover(String message, String device, int priority, String url, String urlTitle)` [Added 1.6+]
- `pushover(String message, String device, int priority, String url, String urlTitle, String soundFile)` [Added 1.6+]
- `pushover(String apiToken, String userKey, String message)`
- `pushover(String apiToken, String userKey, String message, String device)`
- `pushover(String apiToken, String userKey, String message, int priority)`
- `pushover(String apiToken, String userKey, String message, String device, int priority)`
- `pushover(String apiToken, String userKey, String message, String device, String title, String url, String urlTitle, int priority, String soundFile)`

[Table of Contents](#table-of-contents)

### Squeezebox Action

Interact directly with your Squeezebox devices from within rules and scripts. In order to use these actions you must also install the **org.openhab.io.squeezeserver** bundle and configure the 'squeeze' properties in openhab.cfg.

See the [[Squeezebox Action]] page for more details.

[Table of Contents](#table-of-contents)

### TinkerForge Actions

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
         tfClearLCD("d4j")
  end
  ```
1. `tfServoSetposition(String uid, String num, String position, String velocity, String acceleration)`

  Sets the position of a TinkerForge servo with the uid $uid and servo number to the position $position using the speed $speed and acceleration $acceleration.

  Example:
	```
	rule "move servo"
    when
        Item MoveServo received command ON
    then
       tfServoSetposition("6Crt5W", "servo0", "0", "65535", "65535")
       Thread::sleep(1000)
       tfServoSetposition("6Crt5W", "servo0", "-9000", "65535", "65535")
       Thread::sleep(1000)
      tfServoSetposition("6Crt5W", "servo0", "9000", "65535", "65535")
  end
	```
1. `tfDCMotorSetspeed(String uid, String speed, String acceleration, String drivemode)`

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
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
  end
	```
1. `tfDCMotorSetspeed(String uid, Short speed, Integer acceleration, String drivemode)`

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
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
       Thread::sleep(1000)
       tfDCMotorSetspeed("62Zduj", speed, acceleration, "break")
	end
  ```

[Table of Contents](#table-of-contents)

### Twitter Actions

Connect to Twitter through this action (please check openhab.cfg for required configuration settings):
- `sendTweet(String message)`: Sends a Tweet via Twitter
- `sendDirectMessage(String recipient, String message)`: Sends a direct Message via Twitter

See the [[Twitter Action]] page for more details.

[Table of Contents](#table-of-contents)

### Weather Actions

* `getHumidex(double temperature, int hygro)`: Compute the Humidex index given temperature in Celsius and hygrometry (relative percent).  Returns Humidex index value.
* `getBeaufortIndex(double speed)`: Compute the [Beaufort scale](http://en.wikipedia.org/wiki/Beaufort_scale) for a given wind speed in m/s.  Returns the Beaufort Index between 0 and 12.  `transform/beaufort.map`:
```
0=Calm
1=Light air
2=Light breeze
3=Gentle breeze
4=Moderate breeze
5=Fresh breeze
6=Strong breeze
7=High wind
8=Gale
9=Strong/severe gale
10=Storm
11=Violent storm
12=Hurricane force
```
* `getSeaLevelPressure(double pressure, double temp, double altitude)`: Compute the [Sea Level Pressure](http://keisan.casio.com/exec/system/1224575267), given absolute pressure in hPa, temperature in Celsius, and altitude in meters.  Returns equivalent sea level pressure.
* `getWindDirection(int degree)`: Transform an orientation angle (in degrees) to its cardinal string equivalent.  Returns string representing the direction.

[Table of Contents](#table-of-contents)

### XBMC Actions

Sends notifications to XBMC
- `sendXbmcNotification(host, port, title, message)`: Sends a message to a given XBMC instance
- `sendXbmcNotification(host, port, title, message, image, displayTime)`: Sends a message to a given XBMC instance (image=a URL pointing to an image, displayTime=a display time for the message in milliseconds)

[Table of Contents](#table-of-contents)

### XMPP Actions

*configure XMPP (openhab.cfg)*

Example: Google
```
xmpp:servername=talk.google.com
xmpp:securitymode=required
# You need this "tlspin" if openhab cannot verify the certificat from the google server
xmpp:tlspin=CERTSHA256:9e670d6624fc0c451d8d8e3efa81d4d8246ff9354800de09b549700e8d2a730a
xmpp:proxy=gmail.com
xmpp:username=my.openhab@gmail.com
xmpp:password=mysectret
# you may need to add the cryptic talk.google.com address of your private google account to the allowed users
# check you openhab.log to found the address after you send something via hangout to your openhab account
xmpp:consoleusers=**cryptic**@public.talk.google.com,myname@gmail.com
```
*using XMPP*

This add-on provides XMPP communication. Besides the action methods itself, it also contains the XMPP console (please check openhab.cfg for required configuration settings):
- `sendXMPP(String to, String message)`: Sends a message to an XMPP user
- `sendXMPP(String to, String message, String attachmentUrl)`: Sends a message with an attachment to an XMPP user
- `chatXMPP(String message)`: Sends a message to an multi user chat

[Table of Contents](#table-of-contents)