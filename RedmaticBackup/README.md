
# redmatic-flow-misc-RedmaticBackup

Dieser Node Red Flow erzeugt tägliche Backups der `flow.json` und `flow_cred.json` Datei. Für eine Status-Benachrichtigung stehen folgende Wege zur Verfügung:

- Email
- [Dropbox](https://www.dropbox.com)
- [Pushover](https://pushover.net/)

## Zusätzliche Nodes

Es werden folgende zusätzlichen Nodes benötigt:

- [Node-Red-Node-Dropbox](https://flows.nodered.org/node/node-red-node-dropbox) 
- [Node-Red-Node-Pushover](https://www.npmjs.com/package/node-red-node-pushover)

## Flow

<img src="https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/RedmaticBackup/src_readme/flow.png" width="400"/>

### Import Flow
Den Inhalt von [flow.md](https://raw.githubusercontent.com/Sineos/redmatic-flow-misc/master/RedmaticBackup/flow.md) kopieren und in Node-Red importieren.

## Konfiguration der einzelnen Nodes

In der weiteren Erklärung wird auf einzelne Nodes näher eingegangen.

### <a name="settings"></a>Function Node: Backup Settings

Über die vorgeschaltete Inject Node werden im Flow-Context die Einstellungen für das Backup angelegt:

    var backupSettings = {};
    
    backupSettings = {
      'pathBackup': '/media/usb0/redmatic/backup/',
      'fileFlow': '/usr/local/addons/redmatic/var/flows.json',
      'fileFlowCred': '/usr/local/addons/redmatic/var/flows_cred.json',
      'keepBackups': 5,
      'useEmail': true,
      'usePushover': true,
      'useDropbox': true,
      'pushover': {
        'device': 'bla',
        'topicSuccess': 'Redmatic backup successfull',
        'topicFailure': 'Redmatic backup failed',
        'prioritySuccess': 0,
        'priorityFailure': 2,
        'soundSuccess': 'magic',
        'soundFailure': 'spacealarm'
      },
      'email': {
        'to': 'bla@bla.com',
        'subjectSuccess': 'Redmatic backup successfull',
        'subjectFailure': 'Redmatic backup failed',
        'useAttachment': true
      },
      'temp': { //Filled during runtime
        'lastTime': '',
        'lastCommand': '',
        'lastReturnCode': '',
        'lastError': '',
        'fullFile': ''
      }
    };
    
    flow.set('backupSettings', backupSettings);
    
    return null;

- `pathBackup`: Ziel-Pfad zur Ablage des Backups. Hier beispielhaft auf einem USB Drive. Der Pfad wird automatisch angelegt. Pfad muss mit einem / enden.
- `fileFlow`: Kompletter Pfad zur `flow.json` Datei
- `fileFlowCred`: Kompletter Pfad zur `flow_cred.json` Datei
- `keepBackups`: Anzahl der zu behaltenden Backups. 
- `useEmail`: Benachrichtigungen über Erfolg bzw. Fehler via Email versenden
  - `to`: Empfänger
  - `subjectSuccess`: Emailbetreff bei Erfolg
  - `subjectFailure`: Emailbetreff bei Fehler
  - `useAttachment`: Backup als Emailanhang anfügen
 - `usePushover`: Benachrichtigungen über Erfolg bzw. Fehler via Email versenden
   - `device`: Ziel der Benachrichtung
   - `topicSuccess`: Benachrichtungstitel bei Erfolg
   - `topicFailure`: Benachrichtungstitel bei Fehler
   - `prioritySuccess`: Priorität der Meldung bei Erfolg
   - `priorityFailure`: Priorität der Meldung bei Fehler
   - `soundSuccess`: Sound bei Erfolg
   - `soundFailure`: Sound bei Fehler
 - `useDropbox`: Das Backup auf Dropbox hochladen
 - `temp`: Diese werden während der Abarbeitung gefüllt und enthalten die letzten Status bzw. Fehlermeldung für Debug-Zwecke

Die Einstellungen werden mit dem ersten Anlegen des Flows erzeugt. 

### Function Node: Build Commands

Diese Function Node definiert die einzelnen Linux Shell Befehle, die später zur Ausführung gebracht werden. Zu diesem Zeitpunkt sind die Kommandos eine einfache Zeichenkette. Wo notwendig werden die Einstellungen aus den [Backup Settings](#settings) eingefügt.

Die einzelnen Kommandos werden am Schluss in einen Array geschrieben und als `msg.queue` weiter geleitet.

### <a name="exec"></a>Function Node: Manage Command Queue

Sämtliche Kommandos aus der `msg.queue` werden hier der Reihe nach abgearbeitet und an die folgende Exec-Node als `msg.payload` weitergeleitet.

Tritt bei der Abarbeitung ein Fehler auf, werden die restliche Kommandos in der Warteschlange gelöscht und der Fehler im Debug-Fenster über `node.warn` ausgegeben. 
Ebenfalls stehen potentielle Fehler- bzw. Statusmeldungen am zweiten Ausgang dieser Node zur Verfügung und werden dort an die verfügbaren Benachrichtungs-Nodes weitergeleitet.

### Exec Node: Execute Command Queue

Hier werden die einzelnen Payloads ausgeführt. Die Exec Node bzw. die ausgeführten Befehle haben einen sogenannten `Return Code`. In Linux bedeutet eine 0 typischerweise einen erfolgreich ausgeführten Befehl und alles größer als Null einen Fehler. 

Dieser `Return Code` wird an die vorherige [Manage Command Queue](#exec) Node zurück gegeben und dient der Steuerung der Abarbeitung: 
- Null = nächstes Kommando
- Alles größer Null = Verarbeitung abbrechen und Fehler ausgeben
