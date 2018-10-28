# redmatic-flow-misc-SolarLog

Dieser Node Red Flow ist speziell für [RedMatic](https://github.com/hobbyquaker/RedMatic) und dient zum Auslesen eines [Solar-Log](https://flows.nodered.org/node/node-red-contrib-os) PV Monitoring Systems:

 - Auslesen der PV Daten alle 15 Sekunden
 - Berechnung der überschüssigen PV Leistung
 - Einfache Visualisierung
 - Schalten eines Aktors wenn mehr als xx Watt Leistung zur Verfügung stehen

## Zusätzliche Nodes
Für die Systeminformationen wird zusätzlich die [node-red-contrib-moment](https://www.npmjs.com/package/node-red-contrib-moment) benötigt.

## Flow

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/SolarLog/src_readme/flow.png" width="400"/>

### Import Flow
Den Inhalt von [flow.md](https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/SolarLog/flow.md) kopieren und in Node-Red importieren.

## Dashboard

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-sysinfo/master/SolarLog/src_readme/dash.png" width="400"/>
