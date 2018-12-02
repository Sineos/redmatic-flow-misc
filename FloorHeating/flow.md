[
    {
        "id": "791e240.27823dc",
        "type": "subflow",
        "name": "FBH Helper",
        "info": "Provides:\n- Hysteresis function\n- Data conditioning for InfluxDB",
        "category": "",
        "in": [
            {
                "x": 60,
                "y": 140,
                "wires": [
                    {
                        "id": "c4b1280b.1815b"
                    },
                    {
                        "id": "e3d9aee6.c1f62"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 360,
                "y": 140,
                "wires": [
                    {
                        "id": "c4b1280b.1815b",
                        "port": 0
                    }
                ]
            },
            {
                "x": 360,
                "y": 200,
                "wires": [
                    {
                        "id": "e3d9aee6.c1f62",
                        "port": 0
                    }
                ]
            }
        ],
        "outputLabels": [
            "hyst_out",
            "influx_out"
        ]
    },
    {
        "id": "c4b1280b.1815b",
        "type": "unsafe-function",
        "z": "791e240.27823dc",
        "name": "Hysteresis",
        "func": "'use strict';\n// get the flow context hysteresis settings\nvar hystPlus = flow.get('hystPlus') || node.error('No hystPlus found'),\n    hystMinus = flow.get('hystMinus') || node.error('No hystMinus found'),\n    topicActual = flow.get('topicActual') || node.error('No topicActual found'),\n    topicSet = flow.get('topicSet') || node.error('No topicSet found');\n\n// get temperature, target temperature and\n// last hysteresis sate\n// hysteresis state is either 'on' when the upper condition\n// is reached or 'off' for the lower band\nvar curTemp = context.get('curTemp') || null,\n    setTemp = context.get('setTemp') || null,\n    lastState = context.get('lastState') || null;\n\n// complete regex\nvar topicActualRe = new RegExp(topicActual, 'i'),\n    topicSetRe = new RegExp(topicSet, 'i');\n\n// get the actual temperature from incoming msg\n// that match the 'topicActual' regex\nif (topicActualRe.test(msg.topic)) {\n  curTemp = msg.payload;\n  context.set('curTemp', curTemp);\n}\n\n// get the set temperature from incoming msg\n// that match the 'topicSet' regex\nif (topicSetRe.test(msg.topic)) {\n  setTemp = msg.payload;\n  context.set('setTemp', setTemp);\n}\n\n// only continue if both current temperature and\n// target / set temperature is known\nif (curTemp && setTemp) {\n  // if last hysteresis state is unknown send\n  // msg when reaching either lower or upper band\n  if (lastState === null) {\n    if (curTemp >= (setTemp + hystPlus)) {\n      msg.payload = false;\n      context.set('lastState', 'off');\n      return msg;\n    } else if (curTemp <= (setTemp - hystMinus)) {\n      msg.payload = true;\n      context.set('lastState', 'on');\n      return msg;\n    }\n  // if last state is 'on' send message only when\n  // reaching the lower band (= 'off')\n  } else if (lastState === 'on') {\n    if (curTemp >= (setTemp + hystPlus)) {\n      msg.payload = false;\n      context.set('lastState', 'off');\n      return msg;\n    } else if (curTemp <= (setTemp - hystMinus)) {\n      return null;\n    }\n  // if last state is 'off' send message only when\n  // reaching the upper band (= 'on')\n  } else if (lastState === 'off') {\n    if (curTemp >= (setTemp + hystPlus)) {\n      return null;\n    } else if (curTemp <= (setTemp - hystMinus)) {\n      msg.payload = true;\n      context.set('lastState', 'on');\n      return msg;\n    }\n  }\n}\nreturn null;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 210,
        "y": 140,
        "wires": [
            []
        ]
    },
    {
        "id": "e3d9aee6.c1f62",
        "type": "unsafe-function",
        "z": "791e240.27823dc",
        "name": "Prep InfluxDB",
        "func": "'use strict';\n// get the flow context settings\nvar topicActual = flow.get('topicActual') || node.error('No topicActual found'),\n    topicSet = flow.get('topicSet') || node.error('No topicSet found'),\n    topicHumidity = flow.get('topicHumidity') || node.error('No topicHumidity found');\n\n// get temperature, target temperature and\n// humidity from context\nvar curTemp = context.get('curTemp') || null,\n    setTemp = context.get('setTemp') || null,\n    humidity = context.get('humidity') || null,\n    msg2 = {};\n\n// complete regex\nvar topicActualRe = new RegExp(topicActual, 'i'),\n    topicSetRe = new RegExp(topicSet, 'i'),\n    topicHumidityRe = new RegExp(topicHumidity, 'i');\n\n// get the actual temperature from incoming msg\n// that match the 'topicActual' regex\nif (topicActualRe.test(msg.topic)) {\n  curTemp = msg.payload;\n  context.set('curTemp', curTemp);\n}\n\n// get the actual temperature from incoming msg\n// that match the 'topicSet' regex\nif (topicSetRe.test(msg.topic)) {\n  setTemp = msg.payload;\n  context.set('setTemp', setTemp);\n}\n\n// get the actual humidity from incoming msg\n// that match the 'topicHumidity' regex\nif (topicHumidityRe.test(msg.topic)) {\n  humidity = msg.payload;\n  context.set('humidity', humidity);\n}\n\n// only prepare payload if we have actual values\n// for temperature, target temperature and humidity\nif (curTemp && setTemp && humidity) {\n  msg2.payload = [\n    {\n      'tempCur': curTemp,\n      'tempSet': setTemp,\n      'humidity': humidity\n    },\n    {\n      'room': msg.room\n    }\n  ];\n  context.set('curTemp', null);\n  context.set('setTemp', null);\n  context.set('humidity', null);\n  return msg2;\n}\n",
        "outputs": 1,
        "noerr": 0,
        "x": 200,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "c0552248.b955f8",
        "type": "inject",
        "z": "791e240.27823dc",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "x": 130,
        "y": 80,
        "wires": [
            [
                "6a6dad01.2452f4"
            ]
        ]
    },
    {
        "id": "6a6dad01.2452f4",
        "type": "change",
        "z": "791e240.27823dc",
        "name": "Flow Settings",
        "rules": [
            {
                "t": "set",
                "p": "hystPlus",
                "pt": "flow",
                "to": "0.2",
                "tot": "num"
            },
            {
                "t": "set",
                "p": "hystMinus",
                "pt": "flow",
                "to": "0.2",
                "tot": "num"
            },
            {
                "t": "set",
                "p": "topicActual",
                "pt": "flow",
                "to": ".*ACTUAL_TEMPERATURE$",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "topicSet",
                "pt": "flow",
                "to": ".*SET_TEMPERATURE$",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "topicHumidity",
                "pt": "flow",
                "to": ".*ACTUAL_HUMIDITY$",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 320,
        "y": 80,
        "wires": [
            []
        ]
    },
    {
        "id": "b65b2a9a.54eb7",
        "type": "comment",
        "z": "791e240.27823dc",
        "name": "Set variables used in all functions",
        "info": "",
        "x": 170,
        "y": 40,
        "wires": []
    },
    {
        "id": "8d2b7475.b6aaf8",
        "type": "tab",
        "label": "FBH",
        "disabled": false,
        "info": ""
    },
    {
        "id": "e6220d78.1f05b8",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "UG_RT_Bad",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "UG_RT_Bad",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 130,
        "y": 80,
        "wires": [
            [
                "5113d7fb.4ec9e8"
            ]
        ]
    },
    {
        "id": "6d6226d3.d06ac8",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "UG_RT_Keller",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "UG_RT_Keller",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 940,
        "y": 80,
        "wires": [
            [
                "7c9f7907.4e70b"
            ]
        ]
    },
    {
        "id": "acc450fd.af63c8",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "UG_RT_Schlafzimmer",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "UG_RT_Schlafzimmer",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 160,
        "y": 160,
        "wires": [
            [
                "2e499499.cebabc"
            ]
        ]
    },
    {
        "id": "55fd36bd.b3a4c",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "EG_RT_Bad",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "EG_RT_Bad",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 930,
        "y": 160,
        "wires": [
            [
                "251aa1db.601176"
            ]
        ]
    },
    {
        "id": "25c5155c.25ab7a",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "EG_RT_Esszimmer",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "EG_RT_Esszimmer",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 950,
        "y": 240,
        "wires": [
            [
                "2299d34f.5159b4"
            ]
        ]
    },
    {
        "id": "2e09981e.8e4e38",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "EG_RT_Küche",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "EG_RT_Küche",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 140,
        "y": 240,
        "wires": [
            [
                "debb04a8.e345d"
            ]
        ]
    },
    {
        "id": "b5a34214.b63508",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "EG_RT_Wohnzimmer",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "EG_RT_Wohnzimmer",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 160,
        "y": 320,
        "wires": [
            [
                "21ccd3ed.79fdcc"
            ]
        ]
    },
    {
        "id": "98de6766.7d6ae",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "OG_RT_Bad",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "OG_RT_Bad",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 930,
        "y": 320,
        "wires": [
            [
                "4d4f3165.ce9998"
            ]
        ]
    },
    {
        "id": "e6fd000e.5df91",
        "type": "ccu-rpc-event",
        "z": "8d2b7475.b6aaf8",
        "name": "OG_RT_Kueche",
        "iface": "BidCos-RF",
        "ccuConfig": "38263145.35ea0e",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "OG_RT_Kueche",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "THERMALCONTROL_TRANSMIT",
        "channelTypeRx": "str",
        "datapoint": "(HUMIDITY|TEMPERATURE)",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 140,
        "y": 400,
        "wires": [
            [
                "a520bd64.c0d8b"
            ]
        ]
    },
    {
        "id": "1d18b06f.4ef258",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022397:1 UG_FBH_Aktor1_Bad_Flur:1",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 620,
        "y": 80,
        "wires": [
            [
                "1bd91443.01c5f4"
            ]
        ]
    },
    {
        "id": "6f6c72fd.2ef0f4",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022607:4 UG_FBH_Aktor2_Keller:4",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 1410,
        "y": 80,
        "wires": [
            [
                "21ef4d76.beaaca"
            ]
        ]
    },
    {
        "id": "99efdd66.9b0878",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022397:2 UG_FBH_Aktor1_Schlafzimmer:2",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 640,
        "y": 160,
        "wires": [
            [
                "f0d1b38d.c49a48"
            ]
        ]
    },
    {
        "id": "aab4b4ea.15563",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022648:4 EG_FBH_Aktor1_Bad:4",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 1410,
        "y": 160,
        "wires": [
            [
                "74de16b6.56583"
            ]
        ]
    },
    {
        "id": "e60488bd.48ded8",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022457:4 EG_FBH_Aktor2_Esszimmer:4",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 1430,
        "y": 240,
        "wires": [
            [
                "7d1f393d.4c22b8"
            ]
        ]
    },
    {
        "id": "d6a59c17.b9df28",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022457:2 EG_FBH_Aktor2_Küche:2",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 610,
        "y": 240,
        "wires": [
            [
                "db8e8601.763f4"
            ]
        ]
    },
    {
        "id": "132c2cca.18115b",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022457:3 EG_FBH_Aktor2_Wohnzimmer:3",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 640,
        "y": 320,
        "wires": [
            [
                "db973656.3eb988"
            ]
        ]
    },
    {
        "id": "53fd4639.08799",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "NEQ0102417:4 OG_FBH_Aktor_Bad:4",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 1400,
        "y": 320,
        "wires": [
            [
                "15a6a9bb.c2be36"
            ]
        ]
    },
    {
        "id": "e58ff841.11d7f8",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "NEQ0102417:2 OG_FBH_Aktor_Kueche:2",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 620,
        "y": 400,
        "wires": [
            [
                "55ee9ba0.d3370c"
            ]
        ]
    },
    {
        "id": "bfae39bd.edce7",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 475,
        "y": 120,
        "wires": []
    },
    {
        "id": "38cad941.3b59f6",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 475,
        "y": 200,
        "wires": []
    },
    {
        "id": "9ac5f245.bd1d9",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 1275,
        "y": 120,
        "wires": []
    },
    {
        "id": "836cc732.89e02",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 1275,
        "y": 200,
        "wires": []
    },
    {
        "id": "aa722389.da4af8",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 1275,
        "y": 280,
        "wires": []
    },
    {
        "id": "2b444e2.90c2a32",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 475,
        "y": 280,
        "wires": []
    },
    {
        "id": "9b287361.da3398",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 475,
        "y": 360,
        "wires": []
    },
    {
        "id": "673ae9f1.4107d8",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 1275,
        "y": 360,
        "wires": []
    },
    {
        "id": "5a620b0b.a57c4c",
        "type": "influxdb out",
        "z": "8d2b7475.b6aaf8",
        "influxdb": "434a6d08.4db86c",
        "name": "",
        "measurement": "roomClimate",
        "precision": "",
        "retentionPolicy": "",
        "x": 550,
        "y": 540,
        "wires": []
    },
    {
        "id": "ead8f3db.0a5d08",
        "type": "link in",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "fabdf8a8.d69d9",
            "73446794.c8fbe8",
            "836cc732.89e02",
            "673ae9f1.4107d8",
            "c067a1e.b31026",
            "9b287361.da3398",
            "aa722389.da4af8",
            "9ac89e42.618bf8",
            "bc4393e3.29802",
            "2b444e2.90c2a32",
            "9ac5f245.bd1d9",
            "e2e604d8.55d878",
            "a364216d.3b1b9",
            "38cad941.3b59f6",
            "bfae39bd.edce7",
            "cb744a8.6d9ed38",
            "938f12e8.f11a28"
        ],
        "x": 275,
        "y": 580,
        "wires": [
            [
                "5a620b0b.a57c4c",
                "1a7a8e66.d8c23a"
            ]
        ]
    },
    {
        "id": "1669ad0b.26597b",
        "type": "change",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "[\t  {\t    'stateVent': $$.payload ? 1 : 0\t  },\t  {\t    'room': $$.room\t  }\t]",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 220,
        "y": 540,
        "wires": [
            [
                "5a620b0b.a57c4c"
            ]
        ]
    },
    {
        "id": "1bd91443.01c5f4",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 815,
        "y": 80,
        "wires": []
    },
    {
        "id": "bc1d0548.13b26",
        "type": "link in",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "1bd91443.01c5f4",
            "37535961.0c354e",
            "f0d1b38d.c49a48",
            "db973656.3eb988",
            "65ddc813.963ae8",
            "db8e8601.763f4",
            "55ee9ba0.d3370c",
            "f1eaa008.138d08",
            "7d1f393d.4c22b8",
            "74de16b6.56583",
            "21ef4d76.beaaca",
            "73594aec.0ef3ac",
            "b27a043d.65cb58",
            "15a6a9bb.c2be36",
            "990e1b8f.08b47"
        ],
        "x": 75,
        "y": 620,
        "wires": [
            [
                "1669ad0b.26597b",
                "1a7a8e66.d8c23a"
            ]
        ]
    },
    {
        "id": "f0d1b38d.c49a48",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 815,
        "y": 160,
        "wires": []
    },
    {
        "id": "db973656.3eb988",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 815,
        "y": 320,
        "wires": []
    },
    {
        "id": "db8e8601.763f4",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 815,
        "y": 240,
        "wires": []
    },
    {
        "id": "55ee9ba0.d3370c",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 815,
        "y": 400,
        "wires": []
    },
    {
        "id": "7d1f393d.4c22b8",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 1615,
        "y": 240,
        "wires": []
    },
    {
        "id": "74de16b6.56583",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 1615,
        "y": 160,
        "wires": []
    },
    {
        "id": "21ef4d76.beaaca",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 1615,
        "y": 80,
        "wires": []
    },
    {
        "id": "15a6a9bb.c2be36",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "bc1d0548.13b26",
            "ac3011f7.0136d8"
        ],
        "x": 1615,
        "y": 320,
        "wires": []
    },
    {
        "id": "ac3011f7.0136d8",
        "type": "link in",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut2",
        "links": [
            "1bd91443.01c5f4",
            "37535961.0c354e",
            "f0d1b38d.c49a48",
            "db973656.3eb988",
            "65ddc813.963ae8",
            "db8e8601.763f4",
            "55ee9ba0.d3370c",
            "f1eaa008.138d08",
            "7d1f393d.4c22b8",
            "74de16b6.56583",
            "21ef4d76.beaaca",
            "73594aec.0ef3ac",
            "b27a043d.65cb58",
            "15a6a9bb.c2be36",
            "990e1b8f.08b47"
        ],
        "x": 795,
        "y": 540,
        "wires": [
            [
                "42530c15.b51d5c"
            ]
        ]
    },
    {
        "id": "42530c15.b51d5c",
        "type": "combine-logic",
        "z": "8d2b7475.b6aaf8",
        "name": "vent(s) open?",
        "topic": "",
        "operator": "or",
        "defer": 250,
        "timeout": 0,
        "distinction": "topic",
        "x": 920,
        "y": 540,
        "wires": [
            [
                "97531596.9d3a88"
            ]
        ]
    },
    {
        "id": "97531596.9d3a88",
        "type": "ccu-value",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "LEQ0022607:1 UG_FBH_Aktor2_Umwaelzpumpe:1",
        "datapoint": "STATE",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "38263145.35ea0e",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 1190,
        "y": 540,
        "wires": [
            []
        ]
    },
    {
        "id": "1a7a8e66.d8c23a",
        "type": "unsafe-function",
        "z": "8d2b7475.b6aaf8",
        "name": "Prepare UI",
        "func": "'use strict';\nvar uiData = context.get('uiData') || {},\n    msg2 = {},\n    room,\n    uiData2 = {};\n\n// Only the Actors have a room property\n// in their msg object\nif (msg.hasOwnProperty('room')) {\n  room = msg.room;\n  if (!uiData.hasOwnProperty(room)) {\n    uiData[room] = room;\n  }\n  uiData[room] = {\n    'tempCur': (uiData[room].hasOwnProperty('tempCur') ? uiData[room]['tempCur'] : ''),\n    'tempSet': (uiData[room].hasOwnProperty('tempSet') ? uiData[room]['tempSet'] : ''),\n    'humidity': (uiData[room].hasOwnProperty('humidity') ? uiData[room]['humidity'] : ''),\n    'Vent': {\n      'state': (msg.payload ? 'green' : 'red'),\n      'lastTS': (new Date(msg.ts)).toLocaleString('de-DE')\n    }\n  };\n  context.set('uiData', uiData);\n// \"Prep InfluxDB\" has no \"room\" property\n// in the msg object\n} else {\n  room = msg.payload[1].room;\n  if (!uiData.hasOwnProperty(room)) {\n    uiData[room] = room;\n  }\n  uiData[room] = {\n    'Vent': {\n      'state': (uiData[room]['Vent'].hasOwnProperty('state') ? uiData[room]['Vent']['state'] : ''),\n      'lastTS': (uiData[room]['Vent'].hasOwnProperty('lastTS') ? uiData[room]['Vent']['lastTS'] : '')\n    },\n    'tempCur': msg.payload[0].tempCur + ' °C',\n    'tempSet': msg.payload[0].tempSet + ' °C',\n    'humidity': msg.payload[0].humidity + ' %'\n  };\n  context.set('uiData', uiData);\n}\n\n// Sort the object according to the\n// \"room\" keys alphabetically\nObject.keys(uiData).sort().forEach(function(key) {\n  uiData2[key] = uiData[key];\n});\n\nmsg2.payload = uiData2;\nreturn msg2;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 410,
        "y": 620,
        "wires": [
            [
                "7ef1fe35.9574f8"
            ]
        ]
    },
    {
        "id": "7ef1fe35.9574f8",
        "type": "ui_template",
        "z": "8d2b7475.b6aaf8",
        "group": "a398434d.058db",
        "name": "List rooms",
        "order": 0,
        "width": "15",
        "height": "18",
        "format": "<style>\n.led {\n    display: inline-block;\n    text-align: center;\n    vertical-align: middle;\n    padding: 2px;\n    width: 10px;\n    height: 10px;\n    margin: 2px 5px 5px 5px;\n    border-radius: 50%;\n}\n</style>\n\n<h3>Fußbodenheizung</h3>\n<table style=\"text-align: left; width: 100%;\" border=\"1\" cellpadding=\"2\"\n   cellspacing=\"2\">\n   <tbody>\n      <tr>\n         <td style=\"vertical-align: middle; text-align: center; font-weight: bold;\">Raum</td>\n         <td style=\"vertical-align: middle; text-align: center; font-weight: bold;\">Temperatur</td>\n         <td style=\"vertical-align: middle; text-align: center; font-weight: bold;\">SollTemperatur</td>\n         <td style=\"vertical-align: middle; text-align: center; font-weight: bold;\">Luftfeuchtigkeit</td>\n         <td style=\"vertical-align: middle; text-align: center; font-weight: bold;\">Ventilstatus</td>\n         <td style=\"vertical-align: middle; text-align: center; font-weight: bold;\">Letzte Änderung</td>\n      </tr>\n      <tr ng-repeat=\"(key, value) in msg.payload\">\n         <td style=\"vertical-align: middle; text-align: center;\">{{key}}</td>\n         <td style=\"vertical-align: middle; text-align: center;\">{{value['tempCur']}}</td>\n         <td style=\"vertical-align: middle; text-align: center;\">{{value['tempSet']}}</td>\n         <td style=\"vertical-align: middle; text-align: center;\">{{value['humidity']}}</td>\n         <td style=\"vertical-align: middle; text-align: center;\"><div class=\"led\" \n                                                                  style=\"background-color: {{value['Vent']['state']}}; \n                                                                         box-shadow: black 0 -1px 1px 0px, inset black  0 -1px 4px, {{value['Vent']['state']}} 0 3px 15px;\">\n                                                                 </div>\n         </td>\n         <td style=\"vertical-align: middle; text-align: center;\">{{value['Vent']['lastTS']}}</td>\n      </tr>\n   </tbody>\n</table>\n",
        "storeOutMessages": false,
        "fwdInMessages": false,
        "templateScope": "local",
        "x": 590,
        "y": 620,
        "wires": [
            []
        ]
    },
    {
        "id": "2d9e1ee6.10aa72",
        "type": "comment",
        "z": "8d2b7475.b6aaf8",
        "name": "Send values to InfluxDB and UI",
        "info": "",
        "x": 190,
        "y": 500,
        "wires": []
    },
    {
        "id": "b459a79d.ff95e",
        "type": "comment",
        "z": "8d2b7475.b6aaf8",
        "name": "Get relevant values and control the heating vents",
        "info": "",
        "x": 240,
        "y": 40,
        "wires": []
    },
    {
        "id": "181e2808.f47a88",
        "type": "comment",
        "z": "8d2b7475.b6aaf8",
        "name": "Turn off circulating pump if all vents are closed",
        "info": "",
        "x": 950,
        "y": 500,
        "wires": []
    },
    {
        "id": "5113d7fb.4ec9e8",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "x": 370,
        "y": 80,
        "wires": [
            [
                "1d18b06f.4ef258"
            ],
            [
                "bfae39bd.edce7"
            ]
        ]
    },
    {
        "id": "2e499499.cebabc",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "x": 370,
        "y": 160,
        "wires": [
            [
                "99efdd66.9b0878"
            ],
            [
                "38cad941.3b59f6"
            ]
        ]
    },
    {
        "id": "debb04a8.e345d",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "x": 370,
        "y": 240,
        "wires": [
            [
                "d6a59c17.b9df28"
            ],
            [
                "2b444e2.90c2a32"
            ]
        ]
    },
    {
        "id": "21ccd3ed.79fdcc",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "x": 370,
        "y": 320,
        "wires": [
            [
                "132c2cca.18115b"
            ],
            [
                "9b287361.da3398"
            ]
        ]
    },
    {
        "id": "a520bd64.c0d8b",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "x": 370,
        "y": 400,
        "wires": [
            [
                "e58ff841.11d7f8"
            ],
            [
                "938f12e8.f11a28"
            ]
        ]
    },
    {
        "id": "7c9f7907.4e70b",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "x": 1150,
        "y": 80,
        "wires": [
            [
                "6f6c72fd.2ef0f4"
            ],
            [
                "9ac5f245.bd1d9"
            ]
        ]
    },
    {
        "id": "251aa1db.601176",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "x": 1150,
        "y": 160,
        "wires": [
            [
                "aab4b4ea.15563"
            ],
            [
                "836cc732.89e02"
            ]
        ]
    },
    {
        "id": "2299d34f.5159b4",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "name": "",
        "x": 1150,
        "y": 240,
        "wires": [
            [
                "e60488bd.48ded8"
            ],
            [
                "aa722389.da4af8"
            ]
        ]
    },
    {
        "id": "4d4f3165.ce9998",
        "type": "subflow:791e240.27823dc",
        "z": "8d2b7475.b6aaf8",
        "x": 1150,
        "y": 320,
        "wires": [
            [
                "53fd4639.08799"
            ],
            [
                "673ae9f1.4107d8"
            ]
        ]
    },
    {
        "id": "938f12e8.f11a28",
        "type": "link out",
        "z": "8d2b7475.b6aaf8",
        "name": "InfluxOut",
        "links": [
            "ead8f3db.0a5d08"
        ],
        "x": 475,
        "y": 440,
        "wires": []
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
    },
    {
        "id": "434a6d08.4db86c",
        "type": "influxdb",
        "z": "",
        "hostname": "192.168.178.22",
        "port": "8086",
        "protocol": "http",
        "database": "iobroker",
        "name": "ioBrokerDB",
        "usetls": false,
        "tls": ""
    },
    {
        "id": "a398434d.058db",
        "type": "ui_group",
        "z": "",
        "name": "Default",
        "tab": "f099a772.142eb",
        "disp": false,
        "width": "15",
        "collapse": false
    },
    {
        "id": "f099a772.142eb",
        "type": "ui_tab",
        "z": "",
        "name": "FBH",
        "icon": "dashboard"
    }
]