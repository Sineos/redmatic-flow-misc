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
        "id": "f4f82e39.6c0d9",
        "type": "tab",
        "label": "Flow 2",
        "disabled": false,
        "info": ""
    },
    {
        "id": "3405d203.a6e39e",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "a693aa80.dbcba"
            ]
        ]
    },
    {
        "id": "9b125f.688fc5a",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "acb54df8.1e2d28"
            ]
        ]
    },
    {
        "id": "d44413ab.937178",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "37e661fa.6043de"
            ]
        ]
    },
    {
        "id": "4d8964a.181e21c",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "97bc2653.899488"
            ]
        ]
    },
    {
        "id": "87013da0.780b28",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "f029e1e4.86b2"
            ]
        ]
    },
    {
        "id": "8561cf58.746cc",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "4918d9a2.4138c8"
            ]
        ]
    },
    {
        "id": "e09e6774.aef52",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "139e57c.09b6828"
            ]
        ]
    },
    {
        "id": "3563bf07.136438",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "e48ccd3.154153"
            ]
        ]
    },
    {
        "id": "baa3c05.21ae9c",
        "type": "ccu-rpc-event",
        "z": "f4f82e39.6c0d9",
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
                "eb025d6f.eb25c8"
            ]
        ]
    },
    {
        "id": "4176885f.eb269",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "d301ed33.c7c19"
            ]
        ]
    },
    {
        "id": "db6c6d56.d616a",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "d27d5a1e.d0855"
            ]
        ]
    },
    {
        "id": "5fafe224.857f9c",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "5b9895d3.e0709c"
            ]
        ]
    },
    {
        "id": "98d469c7.48b958",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "2eff159c.40123a"
            ]
        ]
    },
    {
        "id": "32b5b9fc.5b3e46",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "1abf9b83.fbdaac"
            ]
        ]
    },
    {
        "id": "adbdf24a.06732",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "f610bb2e.b510a8"
            ]
        ]
    },
    {
        "id": "7984322d.183854",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "b615c383.49c278"
            ]
        ]
    },
    {
        "id": "7a89dbd2.1f9f1c",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "2f066314.3b26dc"
            ]
        ]
    },
    {
        "id": "19cbcdde.9cec1a",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
                "7f3ff8f1.d03e8"
            ]
        ]
    },
    {
        "id": "a917413a.6b1e4",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 475,
        "y": 120,
        "wires": []
    },
    {
        "id": "8480867a.5e81c",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 475,
        "y": 200,
        "wires": []
    },
    {
        "id": "437d4676.9a6d68",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 1275,
        "y": 120,
        "wires": []
    },
    {
        "id": "23d49f50.cbcca8",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 1275,
        "y": 200,
        "wires": []
    },
    {
        "id": "3d680c96.3f1ff4",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 1275,
        "y": 280,
        "wires": []
    },
    {
        "id": "56c8f512.4201bc",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 475,
        "y": 280,
        "wires": []
    },
    {
        "id": "1422db0e.015f0d",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 475,
        "y": 360,
        "wires": []
    },
    {
        "id": "99d5d45d.0abaa8",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "3f0f35fe.e6eb92"
        ],
        "x": 1275,
        "y": 360,
        "wires": []
    },
    {
        "id": "2458c817.88ceb8",
        "type": "influxdb out",
        "z": "f4f82e39.6c0d9",
        "influxdb": "434a6d08.4db86c",
        "name": "",
        "measurement": "roomClimate",
        "precision": "",
        "retentionPolicy": "",
        "x": 550,
        "y": 520,
        "wires": []
    },
    {
        "id": "3f0f35fe.e6eb92",
        "type": "link in",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut",
        "links": [
            "fabdf8a8.d69d9",
            "73446794.c8fbe8",
            "23d49f50.cbcca8",
            "99d5d45d.0abaa8",
            "c067a1e.b31026",
            "1422db0e.015f0d",
            "3d680c96.3f1ff4",
            "9ac89e42.618bf8",
            "bc4393e3.29802",
            "56c8f512.4201bc",
            "437d4676.9a6d68",
            "e2e604d8.55d878",
            "a364216d.3b1b9",
            "8480867a.5e81c",
            "a917413a.6b1e4",
            "cb744a8.6d9ed38"
        ],
        "x": 275,
        "y": 560,
        "wires": [
            [
                "2458c817.88ceb8",
                "61a828e5.a08348"
            ]
        ]
    },
    {
        "id": "f2f2aea.cfaba5",
        "type": "change",
        "z": "f4f82e39.6c0d9",
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
        "y": 520,
        "wires": [
            [
                "2458c817.88ceb8"
            ]
        ]
    },
    {
        "id": "d301ed33.c7c19",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 815,
        "y": 80,
        "wires": []
    },
    {
        "id": "3933b049.546ec8",
        "type": "link in",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "d301ed33.c7c19",
            "37535961.0c354e",
            "5b9895d3.e0709c",
            "b615c383.49c278",
            "65ddc813.963ae8",
            "f610bb2e.b510a8",
            "7f3ff8f1.d03e8",
            "f1eaa008.138d08",
            "1abf9b83.fbdaac",
            "2eff159c.40123a",
            "d27d5a1e.d0855",
            "73594aec.0ef3ac",
            "b27a043d.65cb58",
            "2f066314.3b26dc",
            "990e1b8f.08b47"
        ],
        "x": 75,
        "y": 600,
        "wires": [
            [
                "f2f2aea.cfaba5",
                "61a828e5.a08348"
            ]
        ]
    },
    {
        "id": "5b9895d3.e0709c",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 815,
        "y": 160,
        "wires": []
    },
    {
        "id": "b615c383.49c278",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 815,
        "y": 320,
        "wires": []
    },
    {
        "id": "f610bb2e.b510a8",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 815,
        "y": 240,
        "wires": []
    },
    {
        "id": "7f3ff8f1.d03e8",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 815,
        "y": 400,
        "wires": []
    },
    {
        "id": "1abf9b83.fbdaac",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 1615,
        "y": 240,
        "wires": []
    },
    {
        "id": "2eff159c.40123a",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 1615,
        "y": 160,
        "wires": []
    },
    {
        "id": "d27d5a1e.d0855",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 1615,
        "y": 80,
        "wires": []
    },
    {
        "id": "2f066314.3b26dc",
        "type": "link out",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "3933b049.546ec8",
            "11c12aee.b456dd"
        ],
        "x": 1615,
        "y": 320,
        "wires": []
    },
    {
        "id": "11c12aee.b456dd",
        "type": "link in",
        "z": "f4f82e39.6c0d9",
        "name": "InfluxOut2",
        "links": [
            "d301ed33.c7c19",
            "37535961.0c354e",
            "5b9895d3.e0709c",
            "b615c383.49c278",
            "65ddc813.963ae8",
            "f610bb2e.b510a8",
            "7f3ff8f1.d03e8",
            "f1eaa008.138d08",
            "1abf9b83.fbdaac",
            "2eff159c.40123a",
            "d27d5a1e.d0855",
            "73594aec.0ef3ac",
            "b27a043d.65cb58",
            "2f066314.3b26dc",
            "990e1b8f.08b47"
        ],
        "x": 795,
        "y": 520,
        "wires": [
            [
                "e42335ff.2e82c8"
            ]
        ]
    },
    {
        "id": "e42335ff.2e82c8",
        "type": "combine-logic",
        "z": "f4f82e39.6c0d9",
        "name": "vent(s) open?",
        "topic": "",
        "operator": "or",
        "defer": 250,
        "timeout": 0,
        "distinction": "topic",
        "x": 920,
        "y": 520,
        "wires": [
            [
                "48a41a77.d7a91c"
            ]
        ]
    },
    {
        "id": "48a41a77.d7a91c",
        "type": "ccu-value",
        "z": "f4f82e39.6c0d9",
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
        "y": 520,
        "wires": [
            []
        ]
    },
    {
        "id": "61a828e5.a08348",
        "type": "unsafe-function",
        "z": "f4f82e39.6c0d9",
        "name": "Prepare UI",
        "func": "'use strict';\nvar uiData = context.get('uiData') || {},\n    msg2 = {},\n    room,\n    uiData2 = {};\n\n// Only the Actors have a room property\n// in their msg object\nif (msg.hasOwnProperty('room')) {\n  room = msg.room;\n  uiData[room] = {\n    'tempCur': (uiData[room].hasOwnProperty('tempCur') ? uiData[room]['tempCur'] : ''),\n    'tempSet': (uiData[room].hasOwnProperty('tempSet') ? uiData[room]['tempSet'] : ''),\n    'humidity': (uiData[room].hasOwnProperty('humidity') ? uiData[room]['humidity'] : ''),\n    'Vent': {\n      'state': (msg.payload ? 'green' : 'red'),\n      'lastTS': (new Date(msg.ts)).toLocaleString('de-DE')\n    }\n  };\n  context.set('uiData', uiData);\n// \"Prep InfluxDB\" has no \"room\" property\n// in the msg object\n} else {\n  room = msg.payload[1].room;\n  uiData[room] = {\n    'Vent': (uiData[room].hasOwnProperty('Vent') ? uiData[room]['Vent'] : ''),\n    'tempCur': msg.payload[0].tempCur + ' °C',\n    'tempSet': msg.payload[0].tempSet + ' °C',\n    'humidity': msg.payload[0].humidity + ' %'\n  };\n  context.set('uiData', uiData);\n}\n\n// Sort the object according to the\n// \"room\" keys alphabetically\nObject.keys(uiData).sort().forEach(function(key) {\n  uiData2[key] = uiData[key];\n});\n\nmsg2.payload = uiData2;\nreturn msg2;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 410,
        "y": 600,
        "wires": [
            [
                "d60bd6fd.b03068"
            ]
        ]
    },
    {
        "id": "d60bd6fd.b03068",
        "type": "ui_template",
        "z": "f4f82e39.6c0d9",
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
        "y": 600,
        "wires": [
            []
        ]
    },
    {
        "id": "e4ce76ff.638fb",
        "type": "comment",
        "z": "f4f82e39.6c0d9",
        "name": "Send values to InfluxDB and UI",
        "info": "",
        "x": 190,
        "y": 480,
        "wires": []
    },
    {
        "id": "6b3dedda.14c53c",
        "type": "comment",
        "z": "f4f82e39.6c0d9",
        "name": "Get relevant values and control the heating vents",
        "info": "",
        "x": 240,
        "y": 40,
        "wires": []
    },
    {
        "id": "59f04ef.5c5663",
        "type": "comment",
        "z": "f4f82e39.6c0d9",
        "name": "Turn off circulating pump if all vents are closed",
        "info": "",
        "x": 950,
        "y": 480,
        "wires": []
    },
    {
        "id": "a693aa80.dbcba",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "name": "",
        "x": 370,
        "y": 80,
        "wires": [
            [
                "4176885f.eb269"
            ],
            [
                "a917413a.6b1e4"
            ]
        ]
    },
    {
        "id": "37e661fa.6043de",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 370,
        "y": 160,
        "wires": [
            [
                "5fafe224.857f9c"
            ],
            [
                "8480867a.5e81c"
            ]
        ]
    },
    {
        "id": "4918d9a2.4138c8",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 370,
        "y": 240,
        "wires": [
            [
                "adbdf24a.06732"
            ],
            [
                "56c8f512.4201bc"
            ]
        ]
    },
    {
        "id": "139e57c.09b6828",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 370,
        "y": 320,
        "wires": [
            [
                "7984322d.183854"
            ],
            [
                "1422db0e.015f0d"
            ]
        ]
    },
    {
        "id": "eb025d6f.eb25c8",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 370,
        "y": 400,
        "wires": [
            [
                "19cbcdde.9cec1a"
            ],
            []
        ]
    },
    {
        "id": "acb54df8.1e2d28",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 1150,
        "y": 80,
        "wires": [
            [
                "db6c6d56.d616a"
            ],
            [
                "437d4676.9a6d68"
            ]
        ]
    },
    {
        "id": "97bc2653.899488",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 1150,
        "y": 160,
        "wires": [
            [
                "98d469c7.48b958"
            ],
            [
                "23d49f50.cbcca8"
            ]
        ]
    },
    {
        "id": "f029e1e4.86b2",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 1150,
        "y": 240,
        "wires": [
            [
                "32b5b9fc.5b3e46"
            ],
            [
                "3d680c96.3f1ff4"
            ]
        ]
    },
    {
        "id": "e48ccd3.154153",
        "type": "subflow:791e240.27823dc",
        "z": "f4f82e39.6c0d9",
        "x": 1150,
        "y": 320,
        "wires": [
            [
                "7a89dbd2.1f9f1c"
            ],
            [
                "99d5d45d.0abaa8"
            ]
        ]
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