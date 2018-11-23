# redmatic-flow-misc-FloorHeating

Dieser Node-Red Flow steuert die Ventile einer Fußbodenheizung in Abhängigkeit von Raumthermostaten. Zusätzlich werden folgende Werte an eine InfluxDB gesendet bzw. im Dashboard visualisiert:

- Raumtemperatur
- Solltemperatur
- Luftfeuchtigkeit
- Schaltzustand der Ventile

## Verwendete Hardware

- Raumthermostate: [HM-TC-IT-WM-W-EU](https://www.eq-3.de/produkte/homematic/heizung-und-klima/homematic-funk-wandthermostat-aufputzmontage.html)
- Schaltaktoren: [HM-LC-Sw4-SM](https://www.eq-3.de/produkte/homematic/licht/hm-lc-sw4-sm.html)

## Zusätzliche Nodes

Es werden folgende zusätzlichen Nodes benötigt:

- [node-red-contrib-unsafe-function](https://www.npmjs.com/package/node-red-contrib-unsafe-function)
- [node-red-contrib-influxdb](https://www.npmjs.com/package/node-red-contrib-influxdb)

## Flow

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/FloorHeating/src_readme/flow.png" width="400"/>

### Import Flow
Den Inhalt von [flow.md](https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/FloorHeating/flow.md) kopieren und in Node-Red importieren.

## Dashboard

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/FloorHeating/src_readme/dashboard.png" width="400"/>

## Konfiguration der einzelnen Nodes

In der weiteren Erklärung wird auf einzelne Nodes näher eingegangen.

### RPC Event Node: Raumthermostate

Die RPC Event Nodes greifen den `THERMALCONTROL_TRANSMIT` Channel der Raumthermostate ab. Ihr Datapoint ist eine Regex:

    (HUMIDITY|TEMPERATURE)

Diese schränkt die Nachrichten auf solche ein, die entweder `HUMIDITY` oder `TEMPERATURE` enthalten. Damit werden folgenden Datentelegramme weiter geleitet:

- `ACTUAL_TEMPERATURE`: Momentane Raumtemperatur
- `SET_TEMPERATURE`: Solltemperatur
- `ACTUAL_HUMIDITY`: Momentane Luftfeuchtigkeit
- (`PARTY_TEMPERATURE`): Wird im Flow nicht benutzt

### Sub-Flow: FBH Helper

Um nicht die Funktionalität beliebig oft zu wiederholen und ggf. anpassen zu müssen wurde ein Sub-Flow erzeugt:

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/FloorHeating/src_readme/fbh_helper.png" width="400"/>

Der Sub-Flow hat folgenden Funktionen:
- Hysterese: Eine Hysterese erzeugt eine Differenz zwischen einem unteren und oberen Schaltwert und verhindert das "flattern" des Schaltzustands wenn der Momentanwert sich sehr nahe am Sollwert befindet
- InfluxDB: Aufbereitung der Daten, um sie in einer InfluxDB zu speichern

#### Inject-Node und Change Node: Flow Settings

Diese beiden legen im flow-context einige Variablen an, welche in den beiden function-nodes genutzt werden:

- `hystPlus`: Positive Differenz zum Schaltpunkt. Liegt, z.B., der Schaltpunkt (= Solltemperatur) bei 22°C und `hystPlus` bei 0,5 dann wird das Heizungsventil bei einer momentanen Raumtemperatur von 22,5 °C geschaltet. In diesem Fall auf "aus" 
- `hystMinus`: Negative Differenz zum Schaltpunkt. Liegt, z.B., der Schaltpunkt (= Solltemperatur) bei 22°C und `hystMinus` bei 0,5 dann wird das Heizungsventil bei einer momentanen Raumtemperatur von 21,5 °C geschaltet. In diesem Fall auf "ein". 
- `topicActual` / `topicSet` / `topicHumidity`: Als RegEx formulierte Topic Bezeichnungen unter denen die CCU die jeweiligen Werte für Raumtemperatur, Solltemperatur und Luftfeuchtigkeit sendet. Die RegEx bedeutet in diesem Fall: 
  - `*.` : Egal mit welchen Zeichen das Topic anfängt
  - `ACTUAL_TEMPERATURE`: Nach den beliebigen Zeichen muss `ACTUAL_TEMPERATURE` folgen
  - `$`: `ACTUAL_TEMPERATURE` muss am Ende der Zeichenkette stehen

#### Unsafe-Function Node: Hysteresis

Dieses Javascript stellt die Hysterese-Funktion bereit und arbeitet in diesem Fall als [Zwei-Punkt-Regler](https://de.wikipedia.org/wiki/Zweipunktregler).

#### Unsafe-Function Node: Prep InfluxDB

Diese Funktion sammelt die Temperatur und Luftfeuchtigkeitswerte ein. Wenn alle 3 zur Verfügung stehen, werden diese im geforderten Format für die Übermittlung an die InfluxDB ausgegeben.  

Die InfluxDB Werte werden am zweiten Ausgang des Sub-Flows ausgegeben und von dort über eine `Link`-Node zur `InfluxDB-Out` Node gesendet.

### CCU Value Node: Aktoren

Hier werden die jeweiligen Aktoren-Kanäle geschaltet. 

Die `Link`-Node überträgt den jeweiligen Schaltzustand zur zur `InfluxDB-Out` Node.

### Change Node: set msg.payload

Diese Node wandelt über JSONata die ankommende Payload ins geforderte Format für die InfluxDB

    [
      {
        'stateVent': $$.payload ? 1 : 0
      },
      {
        'room': $$.room
      }
    ]

- `stateVent` ist der Feldschlüssel in der Datenbank. Da Grafana keine Booleschen Werte darstellen kann wird aus der `true` / `false` Payload eine 1 bzw. 0 gemacht.
-  `room`: Hier wird `msg.room` gesetzt. `room` wird in der InfluxDB als sogenannter "Tag" zur Abgrenzung der Werte benutzt.

### Unsafe-Function Node: Prepare UI & Template Node: List rooms

Aufbereitung der Daten und Darstellung im Dashboard

### Logic Node: Vent(s) open? & CCU Value Node: Aktor

Die Schaltstatus der Aktoren werden logisch ODER Verknüpft. Ist einer der Aktoren an (`true`) ist das Ergebnis der Logik-Verknüpfung `true`. Sind alle Aktoren aus (`false`) ist das Ergebnis der Verknüpfung `false`.

Mit dem Logikwert wird der Schaltaktor gesteuert an dem die Umwälzpumpe hängt.

