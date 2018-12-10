[
    {
        "id": "4bf2cea8.81615",
        "type": "tab",
        "label": "iFan2",
        "disabled": false,
        "info": ""
    },
    {
        "id": "4227b531.fca614",
        "type": "mqtt out",
        "z": "4bf2cea8.81615",
        "name": "Publish to iFan",
        "topic": "",
        "qos": "",
        "retain": "",
        "broker": "53e3c07e.9531c",
        "x": 640,
        "y": 160,
        "wires": []
    },
    {
        "id": "a17265ae.908f58",
        "type": "ccu-rpc-event",
        "z": "4bf2cea8.81615",
        "name": "EG_SW6_Wintergarten:3",
        "iface": "",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "OEQ0650679",
        "deviceRx": "str",
        "deviceName": "",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "OEQ0650679:3",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "KEY",
        "channelTypeRx": "str",
        "datapoint": "PRESS",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": false,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 150,
        "y": 140,
        "wires": [
            [
                "3a5706bf.7bf8aa"
            ]
        ]
    },
    {
        "id": "161610bd.1574a7",
        "type": "ccu-rpc-event",
        "z": "4bf2cea8.81615",
        "name": "EG_SW6_Wintergarten:4",
        "iface": "",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "OEQ0650679",
        "deviceRx": "str",
        "deviceName": "",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "OEQ0650679:4",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "KEY",
        "channelTypeRx": "str",
        "datapoint": "PRESS_SHORT",
        "datapointRx": "str",
        "change": false,
        "working": false,
        "cache": false,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 150,
        "y": 200,
        "wires": [
            [
                "ba46cfc4.fbd408"
            ]
        ]
    },
    {
        "id": "ba46cfc4.fbd408",
        "type": "change",
        "z": "4bf2cea8.81615",
        "name": "Toggle POWER1",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "TM_iFan/cmnd/POWER1",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "toggle",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 410,
        "y": 200,
        "wires": [
            [
                "4227b531.fca614"
            ]
        ]
    },
    {
        "id": "dc41c098.dbdc",
        "type": "mqtt-json",
        "z": "4bf2cea8.81615",
        "name": "Subscribe to iFan",
        "topic": "TM_iFan/#",
        "property": "",
        "qos": "0",
        "broker": "53e3c07e.9531c",
        "x": 180,
        "y": 80,
        "wires": [
            [
                "3a5706bf.7bf8aa"
            ]
        ]
    },
    {
        "id": "3a5706bf.7bf8aa",
        "type": "function",
        "z": "4bf2cea8.81615",
        "name": "Control Fan Speed",
        "func": "// Get last recorded fan speed\nvar fanSpeed = context.get('fanSpeed') || 0;\nvar msg2 = {};\n\n// Outgoing topic for the Tasmota command channel\nmsg2.topic = 'TM_iFan/cmnd/FANSPEED';\n\n// Fan speed is published either in STATE or\n// in the RESULT topic. Get the fan speed and\n// save it in the node's context\nif (msg.topic === 'TM_iFan/tele/STATE') {\n  fanSpeed = msg.payload.FanSpeed;\n  context.set('fanSpeed', fanSpeed);\n} else if (msg.topic === 'TM_iFan/stat/RESULT' && msg.hasOwnProperty('FanSpeed')) {\n  fanSpeed = msg.FanSpeed;\n  context.set('fanSpeed', fanSpeed);\n}\n\n// Intercept incoming button presses\n// On SHORT_PRESS increment current \n// fan speed by one\nif ((/PRESS_SHORT$/).test(msg.topic)) {\n  if (fanSpeed < 3) {\n    msg2.payload = fanSpeed + 1;\n    return msg2;\n  // if fan speed is max (3) fall through to 0 (off)\n  } else if (fanSpeed === 3) {\n    msg2.payload = 0;\n    return msg2;\n  }\n// On LONG_PRESS turn off the fan\n} else if ((/PRESS_LONG$/).test(msg.topic)) {\n  msg2.payload = 0;\n  return msg2;\n}\n\n",
        "outputs": 1,
        "noerr": 0,
        "x": 410,
        "y": 140,
        "wires": [
            [
                "4227b531.fca614"
            ]
        ]
    },
    {
        "id": "3072c20b.45a9ce",
        "type": "ccu-rpc",
        "z": "4bf2cea8.81615",
        "name": "",
        "ccuConfig": "38263145.35ea0e",
        "iface": "BidCos-RF",
        "method": "reportValueUsage",
        "params": "[\"OEQ0650679:3\",\"PRESS_SHORT\",1]",
        "topic": "${CCU}/${Interface}/${Method}",
        "x": 310,
        "y": 380,
        "wires": [
            []
        ]
    },
    {
        "id": "e9eb3a60.fc47c",
        "type": "inject",
        "z": "4bf2cea8.81615",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "x": 130,
        "y": 380,
        "wires": [
            [
                "3072c20b.45a9ce"
            ]
        ]
    },
    {
        "id": "b3aaea1a.5c5dc8",
        "type": "inject",
        "z": "4bf2cea8.81615",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "x": 130,
        "y": 440,
        "wires": [
            [
                "18e7ae75.13c09a"
            ]
        ]
    },
    {
        "id": "18e7ae75.13c09a",
        "type": "ccu-rpc",
        "z": "4bf2cea8.81615",
        "name": "",
        "ccuConfig": "38263145.35ea0e",
        "iface": "BidCos-RF",
        "method": "reportValueUsage",
        "params": "[\"OEQ0650679:3\",\"PRESS_LONG\",1]",
        "topic": "${CCU}/${Interface}/${Method}",
        "x": 310,
        "y": 440,
        "wires": [
            []
        ]
    },
    {
        "id": "204d9238.92bb2e",
        "type": "inject",
        "z": "4bf2cea8.81615",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "x": 130,
        "y": 500,
        "wires": [
            [
                "207bce24.74bcb2"
            ]
        ]
    },
    {
        "id": "207bce24.74bcb2",
        "type": "ccu-rpc",
        "z": "4bf2cea8.81615",
        "name": "",
        "ccuConfig": "38263145.35ea0e",
        "iface": "BidCos-RF",
        "method": "reportValueUsage",
        "params": "[\"OEQ0650679:4\",\"PRESS_SHORT\",1]",
        "topic": "${CCU}/${Interface}/${Method}",
        "x": 310,
        "y": 500,
        "wires": [
            []
        ]
    },
    {
        "id": "2c4dbd1e.406202",
        "type": "comment",
        "z": "4bf2cea8.81615",
        "name": "Register reportValueUsage",
        "info": "",
        "x": 150,
        "y": 340,
        "wires": []
    },
    {
        "id": "cefa0788.d18ff",
        "type": "comment",
        "z": "4bf2cea8.81615",
        "name": "Control Fan",
        "info": "",
        "x": 110,
        "y": 40,
        "wires": []
    },
    {
        "id": "53e3c07e.9531c",
        "type": "mqtt-broker",
        "z": "",
        "name": "Local",
        "broker": "192.168.178.8",
        "port": "1883",
        "clientid": "",
        "usetls": false,
        "compatmode": true,
        "keepalive": "60",
        "cleansession": true,
        "birthTopic": "",
        "birthQos": "0",
        "birthPayload": "",
        "closeTopic": "",
        "closeQos": "0",
        "closePayload": "",
        "willTopic": "",
        "willQos": "0",
        "willPayload": ""
    },
    {
        "id": "38263145.35ea0e",
        "type": "ccu-connection",
        "z": "",
        "name": "localhost",
        "host": "localhost",
        "regaEnabled": true,
        "bcrfEnabled": true,
        "iprfEnabled": true,
        "virtEnabled": true,
        "bcwiEnabled": false,
        "cuxdEnabled": false,
        "regaPoll": true,
        "regaInterval": "30",
        "rpcPingTimeout": "60",
        "rpcInitAddress": "127.0.0.1",
        "rpcServerHost": "127.0.0.1",
        "rpcBinPort": "2047",
        "rpcXmlPort": "2048",
        "contextStore": "default"
    }
]