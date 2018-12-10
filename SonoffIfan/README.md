

# redmatic-flow-misc-SonoffIfan

Dieser Node-Red Flow kombiniert einen Homematic Sender mit der Ventilatorsteuerung iFan02 von Sonoff

- Steuerung der Ventilatorgeschwindigkeit mit einem Schalterkanal
- Steuerung Licht mit einem zweiten Kanal


## Verwendete Hardware

- Ventilatorsteuerung: [Sonoff iFan02](https://sonoff.itead.cc/en/products/appliances/sonoff-ifan02) mit der [Tasmota Firmware](https://github.com/arendst/Sonoff-Tasmota/wiki/Sonoff-iFan02)
- Funksender: [HM-PB-6-WM55](https://www.eq-3.de/produkte/homematic/licht/homematic-funk-wandtaster-6-fach-aufputzmontage.html)

### Hinweis zu HM-PB-6-WM55 - Verschlüsselung

Dieser und andere Sender (z.B. HM-Sen-MDIR-WM55 oder HM-PBI-4-FM) haben standardmäßig die verschlüsselte Übertragung aktiviert. Normalerweise werden folgende Events gesendet:

- `PRESS_SHORT`: Kurzer Druck auf den Taster
- `PRESS_LONG`: Langer Druck auf den Taster
- `PRESS_LONG_RELEASE`: Loslassen des Taster bei `PRESS_LONG`

Bei eingeschalteter Verschlüsselung gibt es seltsame Effekte, dass diese Events gar nicht oder zu einem falschen Zeitpunkt ausgelöst werden und stattdessen ein `INSTALL_TEST` gesendet wird. Nach heutigem Kenntnisstand ist das weder ein Problem von Redmatic oder Raspberrymatic, sondern von der Firmware der jeweiligen Sender.
Es wird empfohlen die Verschlüsselung für die jeweiligen Kanäle zu deaktivieren.

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/SonoffIfan/src_readme/aes.png" width="400"/>

### <a name="rvu"></a>Hinweis zu HM-PB-6-WM55 - reportValueUsage

`reportValueUsage` ist eine etwas mysteriöse Methode, mit der der CCU mitgeteilt wird, dass man ein bestimmtes Event benutzen möchte. Dies erfolgt nach dem Schema:

    reportValueUsage("NEQ0xxxxx", "PRESS_SHORT", 1)

Der Flow hier setzt diese Methode jeweils für alle verwendeten Events. In wie weit oder unter welchen Umständen das notwendig ist, ist nicht ganz klar.

Diese Methode löst an dem entsprechenden Gerät ein `CONFIG_PENDING` aus, welches ggf. erst mit einer Betätigung des Tasters bestätigt werden muss.

## Flow

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/SonoffIfan/src_readme/flow.png" width="400"/>

### Import Flow
Den Inhalt von [flow.md](https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/SonoffIfan/flow.md) kopieren und in Node-Red importieren.

## Prinzip MQTT

Tasmota macht die Sonoff Geräte (und viele andere) MQTT fähig. Damit lässt sich über ein einfaches Nachrichtenmodell Informationen / Kommandos austauschen. Hierbei wird unterschieden:

- `Publish`: Ereignisse (neue Sensorwerte oder Schaltzustände) oder auch Kommandos werden von einem Teilnehmer veröffentlicht (publish) und stehen dem MQTT Netzwerk zur Verfügung. Ähnlich wie bei Node Red werden diese Veröffentlichungen einem bestimmten `Topic` zugeordnet.
- `Subscribe`: Ein Teilnehmer der sich für bestimmte Informationen / Kommandos interessiert, abonniert (subscribe) ein `Topic`. Sobald also ein abonniertes `Topic` im Netzwerk veröffentlicht wird, wird dieses an den Abonnenten weitergeleitet
- `Broker`: Der Broker ist die Vermittlungsstelle zwischen den Teilnehmern. Alle veröffentlichten `Topics` gehen an ihn und stehen damit potentiellen Abonnenten zur Verfügung. Nur wenn ein Teilnehmer ein `Topic` aktiv abonniert, leitet der Broker das auch an diesen weiter.

Folgende Prinzipskizze soll das Zusammenspiel zwischen Redmatic und Tasmota verdeutlichen:

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/SonoffIfan/src_readme/mqtt.png" width="400"/>

## Konfiguration der einzelnen Nodes

In der weiteren Erklärung wird auf einzelne Nodes näher eingegangen.

### MQTT In Node: Subscribe to iFan

Hier wird das Topic abonniert, auf dem der Tasmota iFan seine Events veröffentlicht. In diesem Fall `TM_iFan/#`. Der Hash-Tag ist die MQTT Wildcard für "alles Folgende".

### Function Node: Control Fan Speed

Hier werden die Taster-Events der vorangegangen RPC Event Nodes verarbeitet und über diese die Ventilatorgeschwindigkeit gesteuert.

    // Get last recorded fan speed
    var fanSpeed = context.get('fanSpeed') || 0;
    var msg2 = {};
    
    // Outgoing topic for the Tasmota command channel
    msg2.topic = 'TM_iFan/cmnd/FANSPEED';
    
    // Fan speed is published either in STATE or
    // in the RESULT topic. Get the fan speed and
    // save it in the node's context
    if (msg.topic === 'TM_iFan/tele/STATE') {
      fanSpeed = msg.payload.FanSpeed;
      context.set('fanSpeed', fanSpeed);
    } else if (msg.topic === 'TM_iFan/stat/RESULT' && msg.hasOwnProperty('FanSpeed')) {
      fanSpeed = msg.FanSpeed;
      context.set('fanSpeed', fanSpeed);
    }
    
    // Intercept incoming button presses
    // On SHORT_PRESS increment current 
    // fan speed by one
    if ((/PRESS_SHORT$/).test(msg.topic)) {
      if (fanSpeed < 3) {
        msg2.payload = fanSpeed + 1;
        return msg2;
      // if fan speed is max (3) fall through to 0 (off)
      } else if (fanSpeed === 3) {
        msg2.payload = 0;
        return msg2;
      }
    // On LONG_PRESS turn off the fan
    } else if ((/PRESS_LONG$/).test(msg.topic)) {
      msg2.payload = 0;
      return msg2;
    }

### Change Node: Toggle POWER1

Das Licht am Ventilator wird über POWER1 angesprochen. Die Change Node setzt das entsprechende MQTT Topic und sendet den Tasmota `toggle` Befehl. Ist das Licht aus, dann schaltet dieser es an und umgekehrt.

### MQTT Out Node: Publish to iFan

Hier werden die vorher generierten Topics und Befehle an den MQTT Broker gesendet. Dieser leitet sie wiederum an das entsprechende Tasmota Gerät, welches dieses Topic abonniert hat.

### RPC Nodes: reportValueUsage

Setzen der `reportValueUsage` Methode für die einzelnen Kanäle wie [oben](#rvu) beschrieben.
