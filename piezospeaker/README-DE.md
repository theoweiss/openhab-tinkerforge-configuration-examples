## Piezo Speaker Bricklet
Das Piezo Speaker Bricklet Binding unterstützt zwei Modi:
  * Senden von Morse Codes mit konfigurierbarer Frequenz
  * Senden von Tönen mit konfigurierbarer Dauer und Frequenz
Die Konfiguration wird beim Item hinterlegt. Möchtest du unterschiedliche Tonabfolgen verwenden, dann legst du einfach pro Tonsequenz ein Item an. Hier ein Beispiel:

```
Switch Beep      "Beep" { autoupdate="false", tinkerforge="uid=s1c, mode=beep, durations=500|100, frequencies=10|10000, repeat=2" }
Switch Morse      "Morse" { autoupdate="false", tinkerforge="uid=s1c, mode=morse, morsecodes=...---...|---, frequencies=10|10000, repeat=2" }
```
