
# redmatic-flow-misc-CanvasBlind

Dieser Node-Red Flow steuert eine Markise (oder Rolladen) und implementiert folgende Funktionen

- Hoch und runter fahren der Markise mit folgenden Taster Events
	- `PRESS_SHORT`: Bewegen der Markise bis zu den Endlagen. Erneutes Drücken stoppt die Bewegung
	 - `PRESS_LONG` / `PRESS_LONG_RELEASE`: Start der Bewegung bis der Taster losgelassen wird
	 - Dashboard Taster
- Reaktion auf Regen und Wind
	- Reaktion auf Wetterbedingungen aktivier- / deaktivierbar
	- Einfahren der Markise bei Regen oder Wind stärker als X m/s
	- Manuelles Übersteuern der Automatik für eine definierte Zeit 
- Visualisierung und Steuerung über ein Dashboard
	- Icons für Wetterbedingungen. Icons werden bei schlechtem Wetter rot
	- Steuerung der Wetterreaktion bzw. Erlauben einer manuellen Übersteuerung
	- Rückkehr zur automatischen Steuerung nach einer definierten Zeit (in Stunden) 
	- Log mit Statusmeldungen
- Dokumentation der Logik (nur Englisch) in den `Comment` Nodes

## Verwendete Hardware

- Taster: [HM-PB-6-WM55](https://www.eq-3.de/produkte/homematic/licht/homematic-funk-wandtaster-6-fach-aufputzmontage.html)
- Schaltaktor: [HM-LC-Sw1-SM](https://www.eq-3.de/produkte/homematic/schalten-und-messen/hm-lc-sw1-sm.html)
- Wetterstation: [Weatherman](https://www.stall.biz/project/weatherman-die-perfekte-wetterstation-fuer-die-hausautomation)  über MQTT angebunden

### Hinweis zu HM-PB-6-WM55
- Verschlüsselung: Siehe [redmatic-flow-misc-SonoffIfan](https://github.com/Sineos/redmatic-flow-misc/tree/master/SonoffIfan#hinweis-zu-hm-pb-6-wm55---verschl%C3%BCsselung)
- reportValueUsage: Siehe [redmatic-flow-misc-SonoffIfan](https://github.com/Sineos/redmatic-flow-misc/tree/master/SonoffIfan#hinweis-zu-hm-pb-6-wm55---reportvalueusage)

## Zusätzliche Nodes

Es werden folgende zusätzlichen Nodes benötigt:

- [node-red-contrib-moment](https://www.npmjs.com/package/node-red-contrib-moment)

## Flow

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/CanvasBlind/src_readme/flow.png" width="400"/>

### Import Flow
Den Inhalt von [flow.md](https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/CanvasBlind/flow.md) kopieren und in Node-Red importieren.

## Dashboard

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/CanvasBlind/src_readme/dashboard.png" width="400"/>

## Vorbereitung

- Aktivieren von `httpStatic`: Siehe [node-red-contrib-weatherman](https://github.com/Sineos/node-red-contrib-weatherman/blob/master/README_DE.md#httpstatic-aktivieren)
- Der Ordner icon muss in den `httpStatic` Pfad kopiert werden. 
- In der `Flow Settings` node verweist der Pfad der Icons auf `httpStatic/icon`. Ggf. muss der Pfad bei anderen Ordnernamen geändert werden. 
- `settings.json` welches die Nutzung von Node-Red Projects unter Einbeziehung des `httpStatic` Inhalts erlaubt (Projektname in diesem Beispiel ist "Redmatic")

```
{
  "uiPort": 1880,
  "uiHost": "127.0.0.1",
  "flowFile": "flows.json",
  "userDir": "/usr/local/addons/redmatic/var",
  "httpRoot": "/addons/red",
  "httpStatic": "/usr/local/addons/redmatic/var/projects/Redmatic",
  "logging": {
    "ain": {
      "level": "info",
      "metrics": false,
      "audit": false
    }
  },
```
