Ich habe in Homeassistant Alarmo eingebunden um eine Alarmanlage zu haben um mein Haus immer im Blick zu halten

Alarmo kann über Hacs heruntergeladen werden

---
Die Alarmanlage habe ich bei mir in verschiedenen Farben aufgeteilt um auch auf meinem Handy Dashboard zu sehen und zu wissen ob und welcher Modus gerade aktiv ist

grün -> Deaktiviert
Zuhause scharf -> Blauer Rand
Abwesend scharf -> Roter Rand
Nachtmodus -> Lila Rand
Urlaub -> oranger Rand
wird aktiviert -> gelber Rand
Alarm ausgelößt -> dunkelroter Rand
---

Ich habe auch die Sensoren mit eingebunden wenn z.B. ein Fenster geschlossen ist ist das Feld Grün ist ein Fenster auf wird das Feld Rot

Wenn der Alarm ausgelöst wird ( Fenster offen, Kamera erkennt Person,...) wird automatisch eine kritische Meldung an das Handy geschickt

die Einzelnen Dateien sind mit im Ordner

In Alarmo bei Benachrichtigungen muss die Yaml wie folgt bearbeitet werden um eine kritische Meldung zu senden  Beispiel bei Alarm ist augelöst:

```yaml
service: notify.notify
data:
  title: 🚨 Alarmanlage
  message: Der Alarm wurde ausgelöst! {{open_sensors|lang=de}}.
  data:
    push:
      sound:
        name: default
        critical: 1
        volume: 1
