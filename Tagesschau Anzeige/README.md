Tagesschau Nachrichtenanzeige für Home Assistant

Dieses Projekt ermöglicht die Anzeige der neuesten Tagesschau-Nachrichten in Home Assistant. Die Nachrichten werden automatisch abgerufen und in einer Swipe-Card mit Bild, Titel, Beschreibung und Link dargestellt.

Funktionen
	•	Automatische Aktualisierung der letzten zehn Tagesschau-Nachrichten
	•	Anzeige von Titel, Bild, Beschreibung und Link zur vollständigen Nachricht
	•	Swipe-Card mit automatischem Wechsel zwischen den Nachrichten
	•	Daten werden in input_text gespeichert und bleiben nach einem Neustart erhalten

Installation und Einrichtung

1. Konfiguration in configuration.yaml

Füge folgende Zeile in die configuration.yaml ein, um die input_text-Konfiguration auszulagern:

input_text: !include input_text.yaml

Falls input_text.yaml noch nicht existiert, erstelle die Datei.

2. input_text.yaml erstellen

Falls die Datei input_text.yaml noch nicht existiert, erstelle sie und füge diesen Inhalt ein:

tagesschau_titel_1:
  name: Titel 1
  max: 255
tagesschau_beschreibung_1:
  name: Beschreibung 1
  max: 10000
tagesschau_link_1:
  name: Link 1
  max: 255
tagesschau_bild_1:
  name: Bild 1
  max: 255

Dies muss für alle zehn Nachrichten (tagesschau_titel_2, tagesschau_titel_3, …, tagesschau_titel_10) wiederholt werden. siehe input_text.yaml

3. Automatisierung in automations.yaml

Füge folgende Automatisierung in die automations.yaml ein, um die Nachrichten aus dem Tagesschau RSS-Feed automatisch zu speichern:

- alias: "Speichern der letzten 10 Tagesschau Nachrichten"
  trigger:
    platform: event
    event_type: feedreader
  action:
    - variables:
        title: "{{ trigger.event.data.title | default('Kein Titel') }}"
        description: "{{ trigger.event.data.description | default('Keine Beschreibung') }}"
        link: "{{ trigger.event.data.link | default('#') }}"
        image: "{{ trigger.event.data.content | regex_findall('src=\"(.*?)\"') | first | default('https://via.placeholder.com/150') }}"

    - repeat:
        count: 9
        sequence:
          - service: input_text.set_value
            data:
              entity_id: "input_text.tagesschau_titel_{{ 10 - repeat.index }}"
              value: "{{ states('input_text.tagesschau_titel_' ~ (9 - repeat.index)) }}"
          - service: input_text.set_value
            data:
              entity_id: "input_text.tagesschau_beschreibung_{{ 10 - repeat.index }}"
              value: "{{ states('input_text.tagesschau_beschreibung_' ~ (9 - repeat.index)) }}"
          - service: input_text.set_value
            data:
              entity_id: "input_text.tagesschau_link_{{ 10 - repeat.index }}"
              value: "{{ states('input_text.tagesschau_link_' ~ (9 - repeat.index)) }}"
          - service: input_text.set_value
            data:
              entity_id: "input_text.tagesschau_bild_{{ 10 - repeat.index }}"
              value: "{{ states('input_text.tagesschau_bild_' ~ (9 - repeat.index)) }}"

    - service: input_text.set_value
      data:
        entity_id: input_text.tagesschau_titel_1
        value: "{{ title }}"
    - service: input_text.set_value
      data:
        entity_id: input_text.tagesschau_beschreibung_1
        value: "{{ description }}"
    - service: input_text.set_value
      data:
        entity_id: input_text.tagesschau_link_1
        value: "{{ link }}"
    - service: input_text.set_value
      data:
        entity_id: input_text.tagesschau_bild_1
        value: "{{ image }}"

4. Tagesschau-Karte in Lovelace hinzufügen

Füge folgende Swipe-Card in dein Home Assistant Dashboard ein:

type: custom:swipe-card
parameters:
  autoplay:
    delay: 5000
  speed: 800
cards:
  - type: markdown
    content: >
      ### {{ states('input_text.tagesschau_titel_1') | replace('None', 'Keine Nachricht verfügbar') }}  
      ![Bild]({{ states('input_text.tagesschau_bild_1') | default('https://via.placeholder.com/150') }})  
      {{ states('input_text.tagesschau_beschreibung_1') | replace('None', 'Keine Beschreibung verfügbar') }}  
      [Mehr lesen]({{ states('input_text.tagesschau_link_1') | default('#') }})

  - type: markdown
    content: >
      ### {{ states('input_text.tagesschau_titel_2') | replace('None', 'Keine Nachricht verfügbar') }}  
      ![Bild]({{ states('input_text.tagesschau_bild_2') | default('https://via.placeholder.com/150') }})  
      {{ states('input_text.tagesschau_beschreibung_2') | replace('None', 'Keine Beschreibung verfügbar') }}  
      [Mehr lesen]({{ states('input_text.tagesschau_link_2') | default('#') }})

Dies muss für alle zehn Nachrichten (tagesschau_titel_3, tagesschau_titel_4, …, tagesschau_titel_10) wiederholt werden.



Falls die Werte gespeichert werden, ist die Einrichtung abgeschlossen.

Bekannte Probleme und Lösungen

Die Nachrichten werden nicht gespeichert

Überprüfe, ob input_text.tagesschau_titel_1 im Status-Bereich von Home Assistant Werte enthält. Falls nicht, die Automatisierung manuell ausführen.

Die Bilder werden nicht geladen

Stelle sicher, dass der content-Block das src="BILD_URL" korrekt verarbeitet.

Unterstützung

Falls dieses Projekt hilfreich war, gibt es die Möglichkeit einer Unterstützung:

[![Sponsor](https://img.shields.io/badge/GitHub-Sponsor-ff69b4?style=flat&logo=github)](https://github.com/sponsors/cedric-2002)

Meine Datein passend zu diesem Projekt sind mit im Ordner diese können so rauskopiert werden.

Ich habe 3 Karten neben einander und diese unterschiedlich Nummeriert. so habe ich immer 3 Verschiedene News in der Anzeige. Bei Fragen einfach Fragen



Viel Spaß damit