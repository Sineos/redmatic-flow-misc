[
    {
        "id": "2274a26b.629126",
        "type": "tab",
        "label": "SolarLog",
        "disabled": false,
        "info": ""
    },
    {
        "id": "192a9f56.db52e9",
        "type": "json",
        "z": "2274a26b.629126",
        "name": "",
        "property": "payload",
        "action": "",
        "pretty": false,
        "x": 190,
        "y": 380,
        "wires": [
            [
                "924f0f2c.f6e4a"
            ]
        ]
    },
    {
        "id": "8ead863e.7897a",
        "type": "http request",
        "z": "2274a26b.629126",
        "name": "POST SolarLog",
        "method": "POST",
        "ret": "txt",
        "url": "192.168.178.15/getjp",
        "tls": "",
        "x": 160,
        "y": 320,
        "wires": [
            [
                "192a9f56.db52e9"
            ]
        ]
    },
    {
        "id": "ce1828e1.dc8018",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Zeit letzte Aktualisierung",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.100",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "lastUpdateTime",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 550,
        "y": 160,
        "wires": [
            [
                "db8921af.fb75"
            ]
        ]
    },
    {
        "id": "42b978d0.d8f198",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Gesamte Leistung PAC von allen Wechselrichtern und Zählern im Wechselrichtermodus",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.101",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "PAc",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 750,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "a2e3ea47.2b16b",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Gesamte Leistung PDC von allen Wechselrichtern",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.102",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Pdc",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 630,
        "y": 240,
        "wires": [
            [
                "b580ad63.49d9",
                "d4f61efe.7ce48"
            ]
        ]
    },
    {
        "id": "c62a27bb.b4cc08",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Durchschnittliche Spannung UAC der Wechselrichter",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.103",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Uac",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 640,
        "y": 280,
        "wires": [
            []
        ]
    },
    {
        "id": "fd82b2ca.3c2578",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Tagesertrag aller Wechselrichter",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.105",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "yieldDay",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 610,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "57507868.9b311",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter gestriger Tagesertrag aller Wechselrichter",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.106",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "yieldYesterday",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 640,
        "y": 360,
        "wires": [
            []
        ]
    },
    {
        "id": "42012bc9.717bc4",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Monatsertrag aller Wechselrichter",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.107",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "yieldMonth",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 620,
        "y": 400,
        "wires": [
            []
        ]
    },
    {
        "id": "b65a0fef.ee7ca",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Jahresertrag aller Wechselrichter",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.108",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "yieldYear",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 610,
        "y": 440,
        "wires": [
            []
        ]
    },
    {
        "id": "1ab64f96.371a68",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Gesamtertrag aller Wechselrichter",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.109",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "yieldTotal",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 580,
        "y": 480,
        "wires": [
            []
        ]
    },
    {
        "id": "5c6f192f.b5a248",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Momentaner Gesamtverbrauch PAC aller Verbrauchszähler",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.110",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "consPac",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 660,
        "y": 520,
        "wires": [
            [
                "b580ad63.49d9",
                "8cf7fa4e.b9e21"
            ]
        ]
    },
    {
        "id": "69091ca4.b072f4",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Verbrauch aller Verbrauchszähler",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.111",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "consYieldDay",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 610,
        "y": 560,
        "wires": [
            []
        ]
    },
    {
        "id": "64b51ba6.dfb59c",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Verbrauch des gestrigen Tages; alle Verbrauchszähler",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.112",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "consYieldYesterday",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 680,
        "y": 600,
        "wires": [
            []
        ]
    },
    {
        "id": "976ab48f.6c6cf",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Verbrauch des Monats; alle Verbrauchszähler",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.113",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "consYieldMonth",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 650,
        "y": 640,
        "wires": [
            []
        ]
    },
    {
        "id": "d382af4.f00c15",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Verbrauch des Jahres, alle Verbrauchszähler",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.114",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "consYieldYear",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 650,
        "y": 680,
        "wires": [
            []
        ]
    },
    {
        "id": "fb939c4a.ceeec",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Summierter Gesamtverbrauch, alle Verbrauchszähler",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.115",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "consYieldTotal",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 640,
        "y": 720,
        "wires": [
            []
        ]
    },
    {
        "id": "685028b8.8e4b3",
        "type": "function",
        "z": "2274a26b.629126",
        "name": "HTTP Request Packet",
        "func": "msg.headers = {\n    'Content-Length' : '20',\n    'Connection' : 'keep-alive',\n    'Pragma' : 'no-cache', \n    'Cache-Control' : 'no-cache'\n};\nmsg.payload = {\"801\":{\"170\":null}};\nreturn msg;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 140,
        "y": 260,
        "wires": [
            [
                "8ead863e.7897a"
            ]
        ]
    },
    {
        "id": "aed24faf.9cfdf",
        "type": "inject",
        "z": "2274a26b.629126",
        "name": "Alle 15 Sekunden",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "15",
        "crontab": "",
        "once": false,
        "onceDelay": "",
        "x": 150,
        "y": 200,
        "wires": [
            [
                "685028b8.8e4b3"
            ]
        ]
    },
    {
        "id": "924f0f2c.f6e4a",
        "type": "rbe",
        "z": "2274a26b.629126",
        "name": "Nur neue Werte",
        "func": "rbe",
        "gap": "",
        "start": "",
        "inout": "out",
        "property": "payload.801.170.100",
        "x": 160,
        "y": 440,
        "wires": [
            [
                "ce1828e1.dc8018",
                "42b978d0.d8f198",
                "a2e3ea47.2b16b",
                "c62a27bb.b4cc08",
                "fd82b2ca.3c2578",
                "57507868.9b311",
                "42012bc9.717bc4",
                "b65a0fef.ee7ca",
                "1ab64f96.371a68",
                "5c6f192f.b5a248",
                "69091ca4.b072f4",
                "64b51ba6.dfb59c",
                "976ab48f.6c6cf",
                "d382af4.f00c15",
                "fb939c4a.ceeec",
                "b24a6534.9e5838"
            ]
        ]
    },
    {
        "id": "b24a6534.9e5838",
        "type": "change",
        "z": "2274a26b.629126",
        "name": "Installierte Generatorleistung",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.801.170.116",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "totalPower",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 760,
        "wires": [
            []
        ]
    },
    {
        "id": "b580ad63.49d9",
        "type": "function",
        "z": "2274a26b.629126",
        "name": "Verfügbare Leistung",
        "func": "'use strict';\nvar solar = context.get('solar') || {};\n\nswitch (msg.topic) {\ncase 'Pdc':\n  solar.Pdc = msg.payload;\n  context.set('solar', solar);\n  msg = null;\n  break;\ncase 'consPac':\n  solar.consPac = msg.payload;\n  context.set('solar', solar);\n  msg = null;\n  break;\ndefault:\n  msg = null;\n  break;\n}\n\nif(solar.Pdc != null && solar.consPac != null) {\n  var msg2 = {};\n  msg2.payload = parseInt(solar.Pdc) - parseInt(solar.consPac);\n  context.set('solar', null);\n  return msg2;\n}\n",
        "outputs": 1,
        "noerr": 0,
        "x": 1200,
        "y": 360,
        "wires": [
            [
                "26146724.360ac8",
                "cfde9d2f.b4b1e"
            ]
        ]
    },
    {
        "id": "26146724.360ac8",
        "type": "ui_gauge",
        "z": "2274a26b.629126",
        "name": "Verfügbare Leistung Solar",
        "group": "6322d978.aaebd",
        "order": 3,
        "width": 0,
        "height": 0,
        "gtype": "gage",
        "title": "Verfügbare Leistung Solar",
        "label": "W",
        "format": "{{value}}",
        "min": "-10000",
        "max": "10000",
        "colors": [
            "#ff0000",
            "#e6e600",
            "#80ff80"
        ],
        "seg1": "0",
        "seg2": "1000",
        "x": 1450,
        "y": 320,
        "wires": []
    },
    {
        "id": "db8921af.fb75",
        "type": "moment",
        "z": "2274a26b.629126",
        "name": "",
        "topic": "",
        "input": "payload",
        "inputType": "msg",
        "inTz": "Europe/Berlin",
        "adjAmount": 0,
        "adjType": "days",
        "adjDir": "add",
        "format": "YYYY-MM-DD, HH:mm:ss ",
        "locale": "de_DE",
        "output": "payload",
        "outputType": "msg",
        "outTz": "Europe/Berlin",
        "x": 1200,
        "y": 160,
        "wires": [
            [
                "5de37f03.c23bb"
            ]
        ]
    },
    {
        "id": "5de37f03.c23bb",
        "type": "ui_text",
        "z": "2274a26b.629126",
        "group": "6322d978.aaebd",
        "order": 5,
        "width": 0,
        "height": 0,
        "name": "",
        "label": "Letzte Aktualisierung: ",
        "format": "{{msg.payload}}",
        "layout": "row-spread",
        "x": 1440,
        "y": 160,
        "wires": []
    },
    {
        "id": "d4f61efe.7ce48",
        "type": "ui_chart",
        "z": "2274a26b.629126",
        "name": "Momentane Leistung Solar",
        "group": "6322d978.aaebd",
        "order": 1,
        "width": 0,
        "height": 0,
        "label": "Momentane Leistung Solar",
        "chartType": "line",
        "legend": "false",
        "xformat": "HH:mm",
        "interpolate": "linear",
        "nodata": "",
        "dot": false,
        "ymin": "",
        "ymax": "",
        "removeOlder": "12",
        "removeOlderPoints": "",
        "removeOlderUnit": "3600",
        "cutout": 0,
        "useOneColor": false,
        "colors": [
            "#1f77b4",
            "#aec7e8",
            "#ff7f0e",
            "#2ca02c",
            "#98df8a",
            "#d62728",
            "#ff9896",
            "#9467bd",
            "#c5b0d5"
        ],
        "useOldStyle": false,
        "x": 1220,
        "y": 240,
        "wires": [
            [],
            []
        ]
    },
    {
        "id": "8cf7fa4e.b9e21",
        "type": "ui_chart",
        "z": "2274a26b.629126",
        "name": "Momentaner Verbrauch",
        "group": "6322d978.aaebd",
        "order": 2,
        "width": 0,
        "height": 0,
        "label": "Momentaner Verbrauch",
        "chartType": "line",
        "legend": "false",
        "xformat": "HH:mm:ss",
        "interpolate": "linear",
        "nodata": "",
        "dot": false,
        "ymin": "",
        "ymax": "",
        "removeOlder": "12",
        "removeOlderPoints": "",
        "removeOlderUnit": "3600",
        "cutout": 0,
        "useOneColor": false,
        "colors": [
            "#1f77b4",
            "#aec7e8",
            "#ff7f0e",
            "#2ca02c",
            "#98df8a",
            "#d62728",
            "#ff9896",
            "#9467bd",
            "#c5b0d5"
        ],
        "useOldStyle": false,
        "x": 1210,
        "y": 520,
        "wires": [
            [],
            []
        ]
    },
    {
        "id": "cfde9d2f.b4b1e",
        "type": "switch",
        "z": "2274a26b.629126",
        "name": "Mehr als 1000 Watt",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "gt",
                "v": "1000",
                "vt": "num"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 1430,
        "y": 400,
        "wires": [
            [
                "5fff0adb.7cc12c"
            ]
        ]
    },
    {
        "id": "1a139324.6ce4ad",
        "type": "ccu-value",
        "z": "2274a26b.629126",
        "name": "Schalte irgendwas ein",
        "iface": "",
        "channel": "",
        "datapoint": "",
        "mode": "",
        "start": true,
        "change": true,
        "cache": false,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 1840,
        "y": 368,
        "wires": [
            []
        ]
    },
    {
        "id": "5fff0adb.7cc12c",
        "type": "trigger",
        "z": "2274a26b.629126",
        "op1": "1",
        "op2": "0",
        "op1type": "num",
        "op2type": "num",
        "duration": "5",
        "extend": true,
        "units": "min",
        "reset": "",
        "bytopic": "all",
        "name": "Nachlauf",
        "x": 1620,
        "y": 368,
        "wires": [
            [
                "1a139324.6ce4ad",
                "8f4b2af4.2dcaa"
            ]
        ]
    },
    {
        "id": "8f4b2af4.2dcaa",
        "type": "function",
        "z": "2274a26b.629126",
        "name": "LED setzen",
        "func": "if (msg.payload === 0) {\n    msg.payload = \"#FF0000\";\n    msg.topic = \"Irgendwas aus\";\n} else if (msg.payload === 1) {\n    msg.payload = \"#00FF00\";\n    msg.topic = \"Irgendwas an\";\n}\nreturn msg;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 1630,
        "y": 440,
        "wires": [
            [
                "331754b7.e75f4c"
            ]
        ]
    },
    {
        "id": "331754b7.e75f4c",
        "type": "ui_template",
        "z": "2274a26b.629126",
        "group": "6322d978.aaebd",
        "name": "Status LED",
        "order": 4,
        "width": "0",
        "height": "0",
        "format": "<style>\n.led {\n    float: right;\n    padding: 3px;\n    width: 10px;\n    height: 10px;\n    margin: 2px 5px 5px 5px;\n    border-radius: 50%;\n    \n}\n</style>\n\n<div>{{msg.topic}}<span class=\"led\" style=\"background-color: {{msg.payload}}; box-shadow: black 0 -1px 1px 0px, inset black  0 -1px 4px, {{msg.payload}} 0 3px 15px;\"></span></div>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 1810,
        "y": 440,
        "wires": [
            []
        ]
    },
    {
        "id": "6322d978.aaebd",
        "type": "ui_group",
        "z": "",
        "name": "err",
        "tab": "b8d6e268.818d7",
        "disp": false,
        "width": "6",
        "collapse": false
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
        "contextStore": "memory"
    },
    {
        "id": "b8d6e268.818d7",
        "type": "ui_tab",
        "z": "",
        "name": "Solar",
        "icon": "dashboard"
    }
]