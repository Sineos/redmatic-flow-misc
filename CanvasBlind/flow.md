[
    {
        "id": "dd34e985.e423c",
        "type": "tab",
        "label": "Markise",
        "disabled": false,
        "info": ""
    },
    {
        "id": "33740056.556208",
        "type": "ccu-value",
        "z": "dd34e985.e423c",
        "name": "",
        "iface": "BidCos-RF",
        "channel": "OEQxxx:1 EG_Wintergarten_Aktor_Markise:1",
        "datapoint": "",
        "mode": "",
        "start": true,
        "change": false,
        "cache": true,
        "queue": false,
        "on": 0,
        "onType": "undefined",
        "ramp": 0,
        "rampType": "undefined",
        "working": false,
        "ccuConfig": "ce845f38.3a33d8",
        "topic": "${CCU}/${Interface}/${channel}/${datapoint}",
        "x": 1370,
        "y": 1080,
        "wires": [
            []
        ]
    },
    {
        "id": "c68dc7a9.d76eb8",
        "type": "ccu-rpc",
        "z": "dd34e985.e423c",
        "name": "",
        "ccuConfig": "ce845f38.3a33d8",
        "iface": "BidCos-RF",
        "method": "reportValueUsage",
        "params": "[\"OEQxxx:1\",\"PRESS_SHORT\",1]",
        "topic": "${CCU}/${Interface}/${Method}",
        "x": 310,
        "y": 120,
        "wires": [
            []
        ]
    },
    {
        "id": "dc5d4bc9.b01d78",
        "type": "ccu-rpc-event",
        "z": "dd34e985.e423c",
        "name": "EG_SW6_Wintergarten:1",
        "iface": "",
        "ccuConfig": "ce845f38.3a33d8",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "xxx",
        "deviceRx": "str",
        "deviceName": "",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "xxx:1",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "KEY",
        "channelTypeRx": "str",
        "channelIndex": "",
        "channelIndexRx": "str",
        "datapoint": "PRESS",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": false,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 150,
        "y": 1000,
        "wires": [
            [
                "a938d71c.9c55c8"
            ]
        ]
    },
    {
        "id": "7bd7c8d2.c6854",
        "type": "ui_slider",
        "z": "dd34e985.e423c",
        "name": "",
        "label": "",
        "tooltip": "",
        "group": "7d72eec5.181528",
        "order": 4,
        "width": 0,
        "height": 0,
        "passthru": true,
        "outs": "all",
        "topic": "",
        "min": 0,
        "max": "1",
        "step": "0.1",
        "x": 410,
        "y": 480,
        "wires": [
            []
        ]
    },
    {
        "id": "b63b9a85.b4c5f",
        "type": "ccu-rpc-event",
        "z": "dd34e985.e423c",
        "name": "EG_SW6_Wintergarten:2",
        "iface": "",
        "ccuConfig": "ce845f38.3a33d8",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "xxx",
        "deviceRx": "str",
        "deviceName": "",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "xxx:2",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "KEY",
        "channelTypeRx": "str",
        "channelIndex": "",
        "channelIndexRx": "str",
        "datapoint": "PRESS",
        "datapointRx": "re",
        "change": false,
        "working": false,
        "cache": false,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 150,
        "y": 1160,
        "wires": [
            [
                "4a059315.d6bf34"
            ]
        ]
    },
    {
        "id": "60203762.0cbfd8",
        "type": "mqtt in",
        "z": "dd34e985.e423c",
        "name": "Wind Data",
        "topic": "wiffi-wz/0/root/192_168_178_66/w_wind_spitze",
        "qos": "2",
        "datatype": "auto",
        "broker": "1168d453.1330dc",
        "x": 100,
        "y": 1680,
        "wires": [
            [
                "b7ca9c1.28917e"
            ]
        ]
    },
    {
        "id": "6c226c9d.ebcb94",
        "type": "mqtt in",
        "z": "dd34e985.e423c",
        "name": "Rain Data",
        "topic": "wiffi-wz/0/root/192_168_178_66/w_regenmelder",
        "qos": "2",
        "datatype": "auto",
        "broker": "1168d453.1330dc",
        "x": 100,
        "y": 1760,
        "wires": [
            [
                "c9ce8e94.b9592"
            ]
        ]
    },
    {
        "id": "3c3a296f.d9ffee",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Level",
        "rules": [
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.Level",
                "pt": "flow",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 240,
        "y": 740,
        "wires": [
            [
                "9fafe04d.15b95"
            ]
        ]
    },
    {
        "id": "fe404e80.1f00c8",
        "type": "ui_switch",
        "z": "dd34e985.e423c",
        "name": "Manual Override ",
        "label": "Manual Override ",
        "tooltip": "",
        "group": "f07d5d3a.e76188",
        "order": 2,
        "width": 0,
        "height": 0,
        "passthru": true,
        "decouple": "false",
        "topic": "",
        "style": "",
        "onvalue": "true",
        "onvalueType": "bool",
        "onicon": "",
        "oncolor": "",
        "offvalue": "false",
        "offvalueType": "bool",
        "officon": "",
        "offcolor": "",
        "x": 200,
        "y": 420,
        "wires": [
            [
                "c85915d.65ad868"
            ]
        ]
    },
    {
        "id": "c85915d.65ad868",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set OverrideEnabled",
        "rules": [
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.OverrideEnabled",
                "pt": "flow",
                "to": "payload",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "OverrideEnabled",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 480,
        "y": 420,
        "wires": [
            [
                "79aaeba1.97ea44"
            ]
        ]
    },
    {
        "id": "cd55352a.4360f",
        "type": "ui_template",
        "z": "dd34e985.e423c",
        "group": "964ef30e.7bd9c8",
        "name": "Canvas Blind Message Log",
        "order": 1,
        "width": "6",
        "height": "5",
        "format": "<h3>Log</h3>\n<table>\n  <tbody>\n    <tr>\n      <td><strong>TimeStamp</strong></td>\n      <td><strong>Message</strong></td>\n    </tr>\n    <tr ng-repeat=\"value in msg.payload\">\n      <td>{{value.timestamp}}</td>\n      <td>{{value.log}}</td>\n    </tr>\n  </tbody>\n</table>",
        "storeOutMessages": true,
        "fwdInMessages": false,
        "templateScope": "local",
        "x": 720,
        "y": 580,
        "wires": [
            []
        ]
    },
    {
        "id": "2eb519f5.1a971e",
        "type": "function",
        "z": "dd34e985.e423c",
        "name": "Rotate Log",
        "func": "// Get Log content from context\nlet dashboardLog = flow.get('cBlindSettings.var.Log', 'file') || [];\n\n// Only log relevant properties\nconst msgTmp = {};\nmsgTmp.log = msg.log;\nmsgTmp.timestamp = msg.timestamp;\n\n// Add msg.log content to array\ndashboardLog.push(msgTmp);\n\n// Rotate array, delete entries exceeding\n// max number of log entries\nif (dashboardLog.length > flow.get('cBlindSettings.logMsg.logLength', 'file')) {\n  dashboardLog.shift();\n}\n\n// Clear array on 'msg.resetlog'\nif (msg.resetlog) {\n  dashboardLog = [];\n}\n\n// Store Log content in context\nflow.set('cBlindSettings.var.Log', dashboardLog, 'file');\n\n// Send array as msg.payload\nmsg = {};\nmsg.payload = dashboardLog;\nreturn msg;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 490,
        "y": 580,
        "wires": [
            [
                "cd55352a.4360f"
            ]
        ]
    },
    {
        "id": "eba490df.effd18",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "logBlindIn",
        "links": [
            "167fd68f.ed0d01",
            "ebe41bd7.952a08",
            "632b93d2.59f6d4",
            "79513e6b.6e6308",
            "ccf13b2b.31d3b",
            "70c77df4.b3747c",
            "ca06714a.620948",
            "b12b4088.f98668",
            "ef2d71ee.8f9e48"
        ],
        "x": 155,
        "y": 540,
        "wires": [
            [
                "74dd766d.793788"
            ]
        ]
    },
    {
        "id": "b96a0c1c.e1e588",
        "type": "ui_text",
        "z": "dd34e985.e423c",
        "group": "470ead96.ebe19c",
        "order": 1,
        "width": "1",
        "height": "1",
        "name": "cBlind Down",
        "label": "",
        "format": "{{msg.iconDown}}",
        "layout": "col-center",
        "x": 1290,
        "y": 300,
        "wires": []
    },
    {
        "id": "5e3784f6.67f7f4",
        "type": "ui_button",
        "z": "dd34e985.e423c",
        "name": "",
        "group": "7d72eec5.181528",
        "order": 3,
        "width": 0,
        "height": 0,
        "passthru": false,
        "label": "Canvas Blind Up",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "fa-arrow-circle-o-up",
        "payload": "true",
        "payloadType": "bool",
        "topic": "PRESS_SHORT",
        "x": 170,
        "y": 1220,
        "wires": [
            [
                "4a059315.d6bf34"
            ]
        ]
    },
    {
        "id": "d4436fcf.aa233",
        "type": "ui_button",
        "z": "dd34e985.e423c",
        "name": "",
        "group": "7d72eec5.181528",
        "order": 2,
        "width": 0,
        "height": 0,
        "passthru": false,
        "label": "Canvas Blind Stop",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "fa-stop-circle",
        "payload": "true",
        "payloadType": "bool",
        "topic": "stopUI",
        "x": 170,
        "y": 1100,
        "wires": [
            [
                "b9b74823.1f0748"
            ]
        ]
    },
    {
        "id": "f0920ae4.21e4b",
        "type": "ui_button",
        "z": "dd34e985.e423c",
        "name": "",
        "group": "7d72eec5.181528",
        "order": 1,
        "width": 0,
        "height": 0,
        "passthru": false,
        "label": "Canvas Blind Down",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "fa-arrow-circle-o-down",
        "payload": "true",
        "payloadType": "bool",
        "topic": "PRESS_SHORT",
        "x": 170,
        "y": 1060,
        "wires": [
            [
                "a938d71c.9c55c8"
            ]
        ]
    },
    {
        "id": "85339ef6.4fdee",
        "type": "ui_text",
        "z": "dd34e985.e423c",
        "group": "470ead96.ebe19c",
        "order": 3,
        "width": "1",
        "height": "1",
        "name": "cBlind Up",
        "label": "",
        "format": "{{msg.iconUp}}",
        "layout": "col-center",
        "x": 1300,
        "y": 380,
        "wires": []
    },
    {
        "id": "1f302015.db18e8",
        "type": "inject",
        "z": "dd34e985.e423c",
        "name": "Init",
        "topic": "",
        "payload": "true",
        "payloadType": "bool",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": "0.2",
        "x": 870,
        "y": 440,
        "wires": [
            [
                "4cecceb2.b30b98",
                "f770e5bc.effdd8",
                "f400416d.f53ed8",
                "d57c677f.e65388",
                "2eaaa4.54f3955c"
            ]
        ]
    },
    {
        "id": "1c660227.5eb09e",
        "type": "ui_text",
        "z": "dd34e985.e423c",
        "group": "470ead96.ebe19c",
        "order": 2,
        "width": "1",
        "height": "1",
        "name": "cBlind Stop",
        "label": "",
        "format": "{{msg.iconStop}}",
        "layout": "col-center",
        "x": 1290,
        "y": 440,
        "wires": []
    },
    {
        "id": "f7dc420f.a4c94",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "msgIcon",
        "links": [
            "e66d35b6.6fff18"
        ],
        "x": 1095,
        "y": 340,
        "wires": [
            [
                "b96a0c1c.e1e588",
                "85339ef6.4fdee"
            ]
        ]
    },
    {
        "id": "e66d35b6.6fff18",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "msgIcon",
        "links": [
            "f7dc420f.a4c94",
            "43550a3e.778c84"
        ],
        "x": 735,
        "y": 740,
        "wires": []
    },
    {
        "id": "f770e5bc.effdd8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set iconDown",
        "rules": [
            {
                "t": "set",
                "p": "iconDown",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconDownBlack",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1040,
        "y": 300,
        "wires": [
            [
                "b96a0c1c.e1e588"
            ]
        ]
    },
    {
        "id": "f400416d.f53ed8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set iconUp",
        "rules": [
            {
                "t": "set",
                "p": "iconUp",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconUpBlack",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1050,
        "y": 380,
        "wires": [
            [
                "85339ef6.4fdee"
            ]
        ]
    },
    {
        "id": "dd6b1524.bf26f8",
        "type": "ui_text",
        "z": "dd34e985.e423c",
        "group": "f07d5d3a.e76188",
        "order": 3,
        "width": "1",
        "height": "1",
        "name": "cBlind Warning Rain",
        "label": "",
        "format": "{{msg.iconRain}}",
        "layout": "col-center",
        "x": 1260,
        "y": 500,
        "wires": []
    },
    {
        "id": "4293b2de.4ce1d4",
        "type": "ui_text",
        "z": "dd34e985.e423c",
        "group": "f07d5d3a.e76188",
        "order": 4,
        "width": "1",
        "height": "1",
        "name": "cBlind Warning Wind",
        "label": "",
        "format": "{{msg.iconWind}}",
        "layout": "col-center",
        "x": 1260,
        "y": 620,
        "wires": []
    },
    {
        "id": "d57c677f.e65388",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set iconRain",
        "rules": [
            {
                "t": "set",
                "p": "iconRain",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconRainBlack",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1050,
        "y": 500,
        "wires": [
            [
                "dd6b1524.bf26f8"
            ]
        ]
    },
    {
        "id": "2eaaa4.54f3955c",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set iconWind",
        "rules": [
            {
                "t": "set",
                "p": "iconWind",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconWindBlack",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1050,
        "y": 620,
        "wires": [
            [
                "4293b2de.4ce1d4"
            ]
        ]
    },
    {
        "id": "6cde8fc2.67bd48",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "msgIconRain",
        "links": [
            "9edfe11d.5315d"
        ],
        "x": 1095,
        "y": 540,
        "wires": [
            [
                "dd6b1524.bf26f8"
            ]
        ]
    },
    {
        "id": "43550a3e.778c84",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "msgLevel",
        "links": [
            "e66d35b6.6fff18"
        ],
        "x": 55,
        "y": 520,
        "wires": [
            [
                "bd516bec.f6705"
            ]
        ]
    },
    {
        "id": "f2287343.abf1a",
        "type": "ccu-rpc",
        "z": "dd34e985.e423c",
        "name": "",
        "ccuConfig": "ce845f38.3a33d8",
        "iface": "BidCos-RF",
        "method": "reportValueUsage",
        "params": "[\"OEQxxx:2\",\"PRESS_SHORT\",1]",
        "topic": "${CCU}/${Interface}/${Method}",
        "x": 310,
        "y": 180,
        "wires": [
            []
        ]
    },
    {
        "id": "ce8bff15.5ec91",
        "type": "ui_button",
        "z": "dd34e985.e423c",
        "name": "",
        "group": "964ef30e.7bd9c8",
        "order": 0,
        "width": 0,
        "height": 0,
        "passthru": false,
        "label": "Reset Log",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "true",
        "payloadType": "bool",
        "topic": "",
        "x": 110,
        "y": 580,
        "wires": [
            [
                "be21677b.304008"
            ]
        ]
    },
    {
        "id": "be21677b.304008",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set ResetLog",
        "rules": [
            {
                "t": "set",
                "p": "resetlog",
                "pt": "msg",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 300,
        "y": 580,
        "wires": [
            [
                "2eb519f5.1a971e"
            ]
        ]
    },
    {
        "id": "6758db4f.4ca224",
        "type": "function",
        "z": "dd34e985.e423c",
        "name": "Flow Settings",
        "func": "const cBlindSettings = {};\n\n// After initial commit the settings below will only\n// be updated when the version changes\ncBlindSettings.version = '2.3';\n\ncBlindSettings.weather = {\n  'maxWindSpeed':7.2,\n  'overrideTime':0.02\n};\n\ncBlindSettings.logMsg = {\n  'logLength':20,\n  'logWeatherClose': 'Blind up due to weather warning',\n  'logBlocked':'Blind operation blocked due to weather warning',\n  'logOverride':'Manual override of weather warning',\n  'logTimer':'Manual override active for the next ' + cBlindSettings.weather.overrideTime + ' hours',\n  'logTimerExp':'Manual override expired'\n};\n\ncBlindSettings.icons = {\n  'iconRainBlack':'<img src=/addons/red/httpStatic/icon/shade_rain_black.png width=\"48\" height=\"48\">',\n  'iconRainRed':'<img src=/addons/red/httpStatic/icon/shade_rain_red.png width=\"48\" height=\"48\">',\n  'iconWindBlack':'<img src=/addons/red/httpStatic/icon/shade_wind_black.png width=\"48\" height=\"48\">',\n  'iconWindRed':'<img src=/addons/red/httpStatic/icon/shade_wind_red.png width=\"48\" height=\"48\">',\n  'iconUpBlack':'<img src=/addons/red/httpStatic/icon/shade_up_black.png width=\"48\" height=\"48\">',\n  'iconUpGreen':'<img src=/addons/red/httpStatic/icon/shade_up_green.png width=\"48\" height=\"48\">',\n  'iconDownBlack':'<img src=/addons/red/httpStatic/icon/shade_down_black.png width=\"48\" height=\"48\">',\n  'iconDownGreen':'<img src=/addons/red/httpStatic/icon/shade_down_green.png width=\"48\" height=\"48\">',\n  'iconStopBlack':'<img src=/addons/red/httpStatic/icon/shade_stop_black.png width=\"48\" height=\"48\">',\n  'iconStopRed':'<img src=/addons/red/httpStatic/icon/shade_stop_Red.png width=\"48\" height=\"48\">'\n};\n\ncBlindSettings.var = {\n  'Working':false,\n  'Level':0,\n  'WeatherControlEnabled': false,\n  'OverrideEnabled':false,\n  'OverrideActive':false,\n  'warningRain':false,\n  'warningWind':false,\n  'Log': []\n};\n\nif (!flow.get('cBlindSettings.version', 'file') || cBlindSettings.version !== flow.get('cBlindSettings.version', 'file')) {\n  flow.set('cBlindSettings', cBlindSettings, 'file');\n}\n\nmsg.payload = cBlindSettings;\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 320,
        "y": 80,
        "wires": [
            [
                "58a6e613.5325c8",
                "c1472560.7a13a",
                "6032a4cb.df3104"
            ]
        ]
    },
    {
        "id": "1b058ad6.4ce72d",
        "type": "inject",
        "z": "dd34e985.e423c",
        "name": "Init",
        "topic": "",
        "payload": "true",
        "payloadType": "bool",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "x": 130,
        "y": 80,
        "wires": [
            [
                "6758db4f.4ca224",
                "c68dc7a9.d76eb8",
                "f2287343.abf1a"
            ]
        ]
    },
    {
        "id": "4cecceb2.b30b98",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set iconStop",
        "rules": [
            {
                "t": "set",
                "p": "iconStop",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconStopBlack",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1050,
        "y": 440,
        "wires": [
            [
                "1c660227.5eb09e"
            ]
        ]
    },
    {
        "id": "9edfe11d.5315d",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WeatherWarningOut",
        "links": [
            "6cde8fc2.67bd48",
            "de8dbdf5.fc5128"
        ],
        "x": 595,
        "y": 1780,
        "wires": []
    },
    {
        "id": "b951f5cd.066778",
        "type": "ccu-rpc-event",
        "z": "dd34e985.e423c",
        "name": "LEVEL",
        "iface": "",
        "ccuConfig": "ce845f38.3a33d8",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "EG_Wintergarten_Aktor_Markise",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "OEQxxx:1",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "",
        "channelTypeRx": "str",
        "channelIndex": "",
        "channelIndexRx": "str",
        "datapoint": "LEVEL",
        "datapointRx": "str",
        "change": false,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 90,
        "y": 740,
        "wires": [
            [
                "3c3a296f.d9ffee"
            ]
        ]
    },
    {
        "id": "9fafe04d.15b95",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "0",
                "vt": "num"
            },
            {
                "t": "eq",
                "v": "1",
                "vt": "str"
            },
            {
                "t": "btwn",
                "v": "0",
                "vt": "num",
                "v2": "1",
                "v2t": "num"
            }
        ],
        "checkall": "false",
        "repair": false,
        "outputs": 3,
        "x": 450,
        "y": 740,
        "wires": [
            [
                "c25ca25b.70285"
            ],
            [
                "29602711.8b97a8"
            ],
            [
                "6d135e9.65062a"
            ]
        ]
    },
    {
        "id": "c25ca25b.70285",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Level = 0",
        "rules": [
            {
                "t": "set",
                "p": "iconDown",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconDownGreen",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "iconUp",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconUpBlack",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 620,
        "y": 700,
        "wires": [
            [
                "e66d35b6.6fff18"
            ]
        ]
    },
    {
        "id": "6d135e9.65062a",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "0 < Level < 1",
        "rules": [
            {
                "t": "set",
                "p": "iconDown",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconDownGreen",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "iconUp",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconUpGreen",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 610,
        "y": 780,
        "wires": [
            [
                "e66d35b6.6fff18"
            ]
        ]
    },
    {
        "id": "29602711.8b97a8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Level = 1",
        "rules": [
            {
                "t": "set",
                "p": "iconDown",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconDownBlack",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "iconUp",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconUpGreen",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 620,
        "y": 740,
        "wires": [
            [
                "e66d35b6.6fff18"
            ]
        ]
    },
    {
        "id": "b7ca9c1.28917e",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "Wind?",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "lt",
                "v": "#:(file)::cBlindSettings.weather.maxWindSpeed",
                "vt": "flow"
            },
            {
                "t": "gte",
                "v": "#:(file)::cBlindSettings.weather.maxWindSpeed",
                "vt": "flow"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 250,
        "y": 1680,
        "wires": [
            [
                "315909fe.bd0f26"
            ],
            [
                "5a796155.a128a8"
            ]
        ]
    },
    {
        "id": "5a796155.a128a8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Wind Warning True ",
        "rules": [
            {
                "t": "set",
                "p": "iconWind",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconWindRed",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.warningWind",
                "pt": "flow",
                "to": "true",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "warningWind",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "true",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 450,
        "y": 1700,
        "wires": [
            [
                "e11c2008.0b986",
                "ad6cfd13.47c2f8"
            ]
        ]
    },
    {
        "id": "315909fe.bd0f26",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Wind Warning False",
        "rules": [
            {
                "t": "set",
                "p": "iconWind",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconWindBlack",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.warningWind",
                "pt": "flow",
                "to": "false",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "warningWind",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "false",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 440,
        "y": 1660,
        "wires": [
            [
                "e11c2008.0b986"
            ]
        ]
    },
    {
        "id": "c9ce8e94.b9592",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "Rain?",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "true",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "false",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 250,
        "y": 1760,
        "wires": [
            [
                "96a255ca.2d3098"
            ],
            [
                "6087fc8e.48c5d4"
            ]
        ]
    },
    {
        "id": "96a255ca.2d3098",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Rain Warning True",
        "rules": [
            {
                "t": "set",
                "p": "iconRain",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconRainRed",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.warningRain",
                "pt": "flow",
                "to": "true",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "warningRain",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "true",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 450,
        "y": 1740,
        "wires": [
            [
                "9edfe11d.5315d",
                "ad6cfd13.47c2f8"
            ]
        ]
    },
    {
        "id": "6087fc8e.48c5d4",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Rain Warning False",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "warningRain",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "iconRain",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.icons.iconRainBlack",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.warningRain",
                "pt": "flow",
                "to": "false",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "false",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 450,
        "y": 1780,
        "wires": [
            [
                "9edfe11d.5315d"
            ]
        ]
    },
    {
        "id": "e11c2008.0b986",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WeatherWarningOut",
        "links": [
            "f6c08807.597b78",
            "de8dbdf5.fc5128"
        ],
        "x": 595,
        "y": 1660,
        "wires": []
    },
    {
        "id": "f6c08807.597b78",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "msgIconWind",
        "links": [
            "e11c2008.0b986"
        ],
        "x": 1095,
        "y": 580,
        "wires": [
            [
                "4293b2de.4ce1d4"
            ]
        ]
    },
    {
        "id": "9fcc0226.d38ef",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "Blind Level < 1?",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "lt",
                "v": "1",
                "vt": "num"
            },
            {
                "t": "eq",
                "v": "1",
                "vt": "num"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 980,
        "y": 1780,
        "wires": [
            [
                "e428803e.8695f8"
            ],
            [
                "953d60f8.d6a928"
            ]
        ]
    },
    {
        "id": "e1eb2e58.688888",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get Blind Level",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.var.Level",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "BlindLevel",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 980,
        "y": 1720,
        "wires": [
            [
                "9fcc0226.d38ef"
            ]
        ]
    },
    {
        "id": "e428803e.8695f8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Blind is down",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "true",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1170,
        "y": 1760,
        "wires": [
            [
                "a0a5fa7a.c964c"
            ]
        ]
    },
    {
        "id": "953d60f8.d6a928",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Blind is up",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "true",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1170,
        "y": 1820,
        "wires": [
            []
        ]
    },
    {
        "id": "d1307a8b.8f4a6",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Topic for IF",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "button",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 1160,
        "wires": [
            [
                "b6a4d921.05318"
            ]
        ]
    },
    {
        "id": "b6a4d921.05318",
        "type": "combine-if",
        "z": "dd34e985.e423c",
        "topic": "Working",
        "name": "",
        "timeout": 0,
        "x": 760,
        "y": 1160,
        "wires": [
            [
                "2b6fe418.bbd064"
            ],
            [
                "82251c01.54255"
            ]
        ]
    },
    {
        "id": "2b6fe418.bbd064",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Working: Send STOP",
        "rules": [
            {
                "t": "set",
                "p": "datapoint",
                "pt": "msg",
                "to": "STOP",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "1",
                "tot": "num"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.Working",
                "pt": "flow",
                "to": "false",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1040,
        "y": 1080,
        "wires": [
            [
                "33740056.556208",
                "4be34500.0aa3e4"
            ]
        ]
    },
    {
        "id": "82251c01.54255",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Not Working: Send LEVEL = 1",
        "rules": [
            {
                "t": "set",
                "p": "datapoint",
                "pt": "msg",
                "to": "LEVEL",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "1",
                "tot": "num"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.Working",
                "pt": "flow",
                "to": "true",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1070,
        "y": 1160,
        "wires": [
            [
                "33740056.556208",
                "4c10f7bc.89d9b8"
            ]
        ]
    },
    {
        "id": "42f78a4e.e3287c",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Working",
        "rules": [
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.Working",
                "pt": "flow",
                "to": "true",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Working",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 430,
        "y": 820,
        "wires": [
            [
                "151d51cb.c8cc2e"
            ]
        ]
    },
    {
        "id": "bfa6df39.c9f46",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            },
            {
                "t": "false"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 250,
        "y": 840,
        "wires": [
            [
                "42f78a4e.e3287c"
            ],
            [
                "57ae798d.872ea8"
            ]
        ]
    },
    {
        "id": "57ae798d.872ea8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Working",
        "rules": [
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.Working",
                "pt": "flow",
                "to": "false",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Working",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 430,
        "y": 860,
        "wires": [
            [
                "151d51cb.c8cc2e"
            ]
        ]
    },
    {
        "id": "4a059315.d6bf34",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "Datapoint",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "regex",
                "v": "PRESS_SHORT$",
                "vt": "str",
                "case": false
            },
            {
                "t": "regex",
                "v": "PRESS_LONG_RELEASE$",
                "vt": "str",
                "case": false
            },
            {
                "t": "regex",
                "v": "PRESS_LONG$",
                "vt": "str",
                "case": false
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 3,
        "x": 360,
        "y": 1160,
        "wires": [
            [
                "d1307a8b.8f4a6"
            ],
            [
                "d1307a8b.8f4a6"
            ],
            [
                "d1307a8b.8f4a6"
            ]
        ]
    },
    {
        "id": "a938d71c.9c55c8",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "Datapoint",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "regex",
                "v": "PRESS_SHORT$",
                "vt": "str",
                "case": false
            },
            {
                "t": "regex",
                "v": "PRESS_LONG_RELEASE$",
                "vt": "str",
                "case": false
            },
            {
                "t": "regex",
                "v": "PRESS_LONG$",
                "vt": "str",
                "case": false
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 3,
        "x": 360,
        "y": 1000,
        "wires": [
            [
                "ea7096ba.f05918"
            ],
            [
                "ea7096ba.f05918"
            ],
            [
                "ea7096ba.f05918"
            ]
        ]
    },
    {
        "id": "ea7096ba.f05918",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Topic for IF",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "button",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 1000,
        "wires": [
            [
                "cf8309e1.715b8"
            ]
        ]
    },
    {
        "id": "cf8309e1.715b8",
        "type": "combine-if",
        "z": "dd34e985.e423c",
        "topic": "Working",
        "name": "",
        "timeout": 0,
        "x": 760,
        "y": 1000,
        "wires": [
            [
                "fe902e77.360a18"
            ],
            [
                "640de5c5.f5c9f4"
            ]
        ]
    },
    {
        "id": "8550fe29.23b528",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Not Working: Send LEVEL = 0",
        "rules": [
            {
                "t": "set",
                "p": "datapoint",
                "pt": "msg",
                "to": "LEVEL",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "0",
                "tot": "num"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.Working",
                "pt": "flow",
                "to": "true",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1070,
        "y": 1000,
        "wires": [
            [
                "33740056.556208",
                "6b18156c.be39fc"
            ]
        ]
    },
    {
        "id": "b9b74823.1f0748",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "BlindStopIn",
        "links": [
            "db0e2faf.98d8c8"
        ],
        "x": 315,
        "y": 1100,
        "wires": []
    },
    {
        "id": "c23af2b.1a2ce1",
        "type": "ccu-rpc-event",
        "z": "dd34e985.e423c",
        "name": "WORKING",
        "iface": "",
        "ccuConfig": "ce845f38.3a33d8",
        "rooms": "",
        "roomsRx": "str",
        "functions": "",
        "functionsRx": "str",
        "device": "",
        "deviceRx": "str",
        "deviceName": "EG_Wintergarten_Aktor_Markise",
        "deviceNameRx": "str",
        "deviceType": "",
        "deviceTypeRx": "str",
        "channel": "OEQxxx:1",
        "channelRx": "str",
        "channelName": "",
        "channelNameRx": "str",
        "channelType": "",
        "channelTypeRx": "str",
        "channelIndex": "",
        "channelIndexRx": "str",
        "datapoint": "WORKING",
        "datapointRx": "str",
        "change": true,
        "working": false,
        "cache": true,
        "topic": "${CCU}/${Interface}/${channelName}/${datapoint}",
        "x": 100,
        "y": 840,
        "wires": [
            [
                "bfa6df39.c9f46"
            ]
        ]
    },
    {
        "id": "695bc391.999c7c",
        "type": "combine-logic",
        "z": "dd34e985.e423c",
        "name": "",
        "topic": "",
        "operator": "or",
        "defer": 250,
        "timeout": 0,
        "distinction": "topic",
        "x": 340,
        "y": 1400,
        "wires": [
            [
                "aa1e2fd5.149e68"
            ]
        ]
    },
    {
        "id": "aa1e2fd5.149e68",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Topic for IF",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "WarningWeather",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 520,
        "y": 1400,
        "wires": [
            [
                "c9d6c735.0e6e78"
            ]
        ]
    },
    {
        "id": "c9d6c735.0e6e78",
        "type": "combine-if",
        "z": "dd34e985.e423c",
        "topic": "WarningWeather",
        "name": "",
        "timeout": 0,
        "x": 510,
        "y": 1460,
        "wires": [
            [
                "1a411add.2e385d"
            ],
            [
                "164bc45f.e6afb4"
            ]
        ]
    },
    {
        "id": "164bc45f.e6afb4",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WeatherControlOut",
        "links": [
            "3e3ee8d8.e1a78"
        ],
        "x": 575,
        "y": 1500,
        "wires": []
    },
    {
        "id": "e7c43e81.5d6d98",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "BlindDownIn",
        "links": [
            "640de5c5.f5c9f4"
        ],
        "x": 55,
        "y": 1460,
        "wires": [
            [
                "4f5302f.1ed40fc"
            ]
        ]
    },
    {
        "id": "640de5c5.f5c9f4",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "BlindDownOut",
        "links": [
            "e7c43e81.5d6d98"
        ],
        "x": 875,
        "y": 1000,
        "wires": []
    },
    {
        "id": "3e3ee8d8.e1a78",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "WeatherControlIn",
        "links": [
            "3baae7aa.92a0f8",
            "164bc45f.e6afb4",
            "215ba6d1.12c322"
        ],
        "x": 935,
        "y": 960,
        "wires": [
            [
                "8550fe29.23b528"
            ]
        ]
    },
    {
        "id": "ecffd9da.c742f",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get overrideTime",
        "rules": [
            {
                "t": "set",
                "p": "delay",
                "pt": "msg",
                "to": "$flowContext('cBlindSettings', 'file').weather.overrideTime * 3600 * 1000",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.OverrideActive",
                "pt": "flow",
                "to": "true",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "OverrideActive",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1050,
        "y": 1340,
        "wires": [
            [
                "cb262167.4f2ca8",
                "61b0bc1a.f85704",
                "b36c33d1.280018"
            ]
        ]
    },
    {
        "id": "1a411add.2e385d",
        "type": "combine-if",
        "z": "dd34e985.e423c",
        "topic": "OverrideEnabled",
        "name": "",
        "timeout": 0,
        "x": 750,
        "y": 1460,
        "wires": [
            [
                "ecffd9da.c742f",
                "3baae7aa.92a0f8"
            ],
            [
                "3a6c3ced.e70454"
            ]
        ]
    },
    {
        "id": "3baae7aa.92a0f8",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WeatherControlOut",
        "links": [
            "3e3ee8d8.e1a78"
        ],
        "x": 895,
        "y": 1340,
        "wires": []
    },
    {
        "id": "b36c33d1.280018",
        "type": "delay",
        "z": "dd34e985.e423c",
        "name": "Delay OverrideActive",
        "pauseType": "delayv",
        "timeout": "5",
        "timeoutUnits": "seconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "x": 1040,
        "y": 1400,
        "wires": [
            [
                "3aa308bb.a7489"
            ]
        ]
    },
    {
        "id": "3d052c84.f6a604",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "BlindUpIn",
        "links": [
            "addf81d0.0fce98",
            "76029476.4e8354"
        ],
        "x": 955,
        "y": 1200,
        "wires": [
            [
                "82251c01.54255"
            ]
        ]
    },
    {
        "id": "33ac1fd3.9248c",
        "type": "comment",
        "z": "dd34e985.e423c",
        "name": "Weather Control I",
        "info": "\nFollowing Logic is applied:\n\n 1. Check if `WeatherControlEnabled` is `true`\n\t - If `false` then send the command to move the Blind down\n\t - If `true` continue the flow\n 2. Check if `warningWind` OR `warningRain` are `true`\n\t - If they are `false` then send the command to move the Blind down\n\t - If `true` continue the flow\n 3. Check if a manual override is allowed (Flow Variable: `OverrideEnabled == true`)\n\t - If `false` then block any actor movement and add a log message\n\t - If `true` continue the flow\n 4. Allow the Blind to be moved down\n 5. Get the grace period (Flow Variable: `overrideTime`) how long the manual override shall be active (See _Flow Settings_)\n 6. Set the Flow Variable `OverrideActive` to `true`\n 7. Add a log messages that the override is active \n 8. Start a timer\n 9. Once the timer is expired, set `OverrideActive` to `false`\n 10. Add a log messages that the override is expired\n\nIf the bad weather continues the next **Weather Data** will move the Blind up, due to `OverrideActive` not set (`false`). See _Weather Control II_",
        "x": 100,
        "y": 1280,
        "wires": []
    },
    {
        "id": "3a6c3ced.e70454",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get Log 'logBlocked'",
        "rules": [
            {
                "t": "set",
                "p": "log",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.logMsg.logBlocked",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1040,
        "y": 1460,
        "wires": [
            [
                "ebe41bd7.952a08"
            ]
        ]
    },
    {
        "id": "ebe41bd7.952a08",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "logBlindOut",
        "links": [
            "eba490df.effd18"
        ],
        "x": 1195,
        "y": 1460,
        "wires": []
    },
    {
        "id": "27e5c75a.f2e3c",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get Log 'logTimerExp'",
        "rules": [
            {
                "t": "set",
                "p": "log",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.logMsg.logTimerExp",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1520,
        "y": 1400,
        "wires": [
            [
                "632b93d2.59f6d4"
            ]
        ]
    },
    {
        "id": "632b93d2.59f6d4",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "logBlindOut",
        "links": [
            "eba490df.effd18"
        ],
        "x": 1595,
        "y": 1440,
        "wires": []
    },
    {
        "id": "cb262167.4f2ca8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get Log 'logTimer'",
        "rules": [
            {
                "t": "set",
                "p": "log",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.logMsg.logTimer",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1290,
        "y": 1340,
        "wires": [
            [
                "70c77df4.b3747c"
            ]
        ]
    },
    {
        "id": "70c77df4.b3747c",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "logBlindOut",
        "links": [
            "eba490df.effd18"
        ],
        "x": 1415,
        "y": 1340,
        "wires": []
    },
    {
        "id": "181bfac7.847afd",
        "type": "ui_switch",
        "z": "dd34e985.e423c",
        "name": "Weather Control",
        "label": "Weather Control",
        "tooltip": "",
        "group": "f07d5d3a.e76188",
        "order": 1,
        "width": 0,
        "height": 0,
        "passthru": true,
        "decouple": "false",
        "topic": "",
        "style": "",
        "onvalue": "true",
        "onvalueType": "bool",
        "onicon": "",
        "oncolor": "",
        "offvalue": "false",
        "offvalueType": "bool",
        "officon": "",
        "offcolor": "",
        "x": 200,
        "y": 300,
        "wires": [
            [
                "80d878b6.324d18",
                "3c4abd87.9a43a2"
            ]
        ]
    },
    {
        "id": "80d878b6.324d18",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set WeatherControlEnabled",
        "rules": [
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.WeatherControlEnabled",
                "pt": "flow",
                "to": "payload",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "WeatherControlEnabled",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 460,
        "y": 300,
        "wires": [
            [
                "a5753d99.437e5"
            ]
        ]
    },
    {
        "id": "4f5302f.1ed40fc",
        "type": "combine-if",
        "z": "dd34e985.e423c",
        "topic": "WeatherControlEnabled",
        "name": "",
        "timeout": 0,
        "x": 230,
        "y": 1460,
        "wires": [
            [
                "c9d6c735.0e6e78"
            ],
            [
                "215ba6d1.12c322"
            ]
        ]
    },
    {
        "id": "215ba6d1.12c322",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WeatherControlOut",
        "links": [
            "3e3ee8d8.e1a78"
        ],
        "x": 315,
        "y": 1500,
        "wires": []
    },
    {
        "id": "c32a697c.84aa1",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Enable",
        "rules": [
            {
                "t": "set",
                "p": "enabled",
                "pt": "msg",
                "to": "$.payload",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "''",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 510,
        "y": 340,
        "wires": [
            [
                "fe404e80.1f00c8"
            ]
        ]
    },
    {
        "id": "3c4abd87.9a43a2",
        "type": "switch",
        "z": "dd34e985.e423c",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            },
            {
                "t": "false"
            }
        ],
        "checkall": "false",
        "repair": false,
        "outputs": 2,
        "x": 370,
        "y": 360,
        "wires": [
            [
                "c32a697c.84aa1"
            ],
            [
                "370871a3.ca8b5e"
            ]
        ]
    },
    {
        "id": "370871a3.ca8b5e",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Disable",
        "rules": [
            {
                "t": "set",
                "p": "enabled",
                "pt": "msg",
                "to": "$.payload",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 510,
        "y": 380,
        "wires": [
            [
                "fe404e80.1f00c8"
            ]
        ]
    },
    {
        "id": "bd516bec.f6705",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Direction",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "1 - $.payload",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "enabled",
                "pt": "msg",
                "to": "false",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 210,
        "y": 480,
        "wires": [
            [
                "7bd7c8d2.c6854"
            ]
        ]
    },
    {
        "id": "3aa308bb.a7489",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set OverrideActive",
        "rules": [
            {
                "t": "set",
                "p": "#:(file)::cBlindSettings.var.OverrideActive",
                "pt": "flow",
                "to": "false",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "false",
                "tot": "bool"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1290,
        "y": 1400,
        "wires": [
            [
                "27e5c75a.f2e3c",
                "707d4943.40511"
            ]
        ]
    },
    {
        "id": "76029476.4e8354",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "BlindUpOut",
        "links": [
            "3d052c84.f6a604"
        ],
        "x": 1495,
        "y": 1760,
        "wires": []
    },
    {
        "id": "d93410a5.805638",
        "type": "comment",
        "z": "dd34e985.e423c",
        "name": "Weather Control II",
        "info": "Following Logic is applied:\n\n 1. Read the Weather Data. This flow uses\n\t - MQTT data telegrams from a Weatherman\n\t - Rain condition is `true` or `false`\n\t - Wind is measured in m/s. See *Flow Settings* `maxWindSpeed`\n 2. Wind and Rain conditions are translated into Boolean and respective Dashboard icons are set\n 3. Check if `WeatherControlEnabled` is `true`\n\t - If `false` the flow ends\n\t - If `true` continue the flow\n 4. Get the current Blind level and check is the Blind is fully up (Level == 1) or any degree down (Level < 1)\n\t - If fully up, the flow ends\n\t - If any degree down the flow continues\n 5. Check if `OverrideActive` is `true`\n 6. Set the Flow Variable `OverrideActive` to `true`\n\t - If `true` the flow ends\n\t - If `false` continue the flow \n 7. Send the command to move the Blind up\n 8. Add a log message that the Blind was moved up due to bad weather\n",
        "x": 110,
        "y": 1600,
        "wires": []
    },
    {
        "id": "ad6cfd13.47c2f8",
        "type": "combine-if",
        "z": "dd34e985.e423c",
        "topic": "WeatherControlEnabled",
        "name": "",
        "timeout": 0,
        "x": 770,
        "y": 1720,
        "wires": [
            [
                "e1eb2e58.688888"
            ],
            []
        ]
    },
    {
        "id": "a0a5fa7a.c964c",
        "type": "combine-if",
        "z": "dd34e985.e423c",
        "topic": "OverrideActive",
        "name": "",
        "timeout": 0,
        "x": 1360,
        "y": 1760,
        "wires": [
            [],
            [
                "76029476.4e8354",
                "32e1d555.602e42"
            ]
        ]
    },
    {
        "id": "a5753d99.437e5",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WeatherControlEnabledOut",
        "links": [
            "be1807aa.f22c8",
            "cfd835fc.d5c3c8",
            "1de95a8f.d67c5d"
        ],
        "x": 635,
        "y": 300,
        "wires": []
    },
    {
        "id": "be1807aa.f22c8",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "WeatherControlEnabledIn",
        "links": [
            "a5753d99.437e5"
        ],
        "x": 135,
        "y": 1400,
        "wires": [
            [
                "4f5302f.1ed40fc"
            ]
        ]
    },
    {
        "id": "cfd835fc.d5c3c8",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "WeatherControlEnabledIn",
        "links": [
            "a5753d99.437e5"
        ],
        "x": 675,
        "y": 1660,
        "wires": [
            [
                "ad6cfd13.47c2f8"
            ]
        ]
    },
    {
        "id": "79aaeba1.97ea44",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "OverrideEnabledOut",
        "links": [
            "a86e9dab.f05868",
            "5a1ed460.e00344"
        ],
        "x": 635,
        "y": 420,
        "wires": []
    },
    {
        "id": "a86e9dab.f05868",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "OverrideEnabledIn",
        "links": [
            "79aaeba1.97ea44"
        ],
        "x": 675,
        "y": 1400,
        "wires": [
            [
                "1a411add.2e385d"
            ]
        ]
    },
    {
        "id": "61b0bc1a.f85704",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "OverrideActiveOut",
        "links": [
            "a50e8d5a.0540c8"
        ],
        "x": 1115,
        "y": 1300,
        "wires": []
    },
    {
        "id": "a50e8d5a.0540c8",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "OverrideActiveIn",
        "links": [
            "61b0bc1a.f85704",
            "707d4943.40511"
        ],
        "x": 1295,
        "y": 1720,
        "wires": [
            [
                "a0a5fa7a.c964c"
            ]
        ]
    },
    {
        "id": "707d4943.40511",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "OverrideActiveOut",
        "links": [
            "a50e8d5a.0540c8"
        ],
        "x": 1355,
        "y": 1440,
        "wires": []
    },
    {
        "id": "80d43947.49faf8",
        "type": "comment",
        "z": "dd34e985.e423c",
        "name": "Init",
        "info": "## Flow Settings\n - Initializes the variables used throughout the flow. \n - Serves as a permanent storage for states and variables, e.g. buttons on the Dashboard or current Level of the Blind etc.\n - The Settings are stored in Node-Red’s file context and will thus survive restarts or reboots\n\n## ReportValueUsage\n - Switches that are used in a HomeMatic program automatically call the `ReprotValueUsage` method. \n - If it is strictly needed or which effect it actually has, is not clear. \n - One effect on the used HM-PB-6-WM55 is that a pressed button is not signaled only via the orange LED but also with a green one. \n",
        "x": 70,
        "y": 40,
        "wires": []
    },
    {
        "id": "72197796.9c5f68",
        "type": "comment",
        "z": "dd34e985.e423c",
        "name": "Dashboard",
        "info": "## Weather Control\n - Turning Weather Control on, makes the Blind automatically react on bad weather conditions (rain or wind)\n - Moving the Blind down with active Weather Warning is blocked\n - Should the Blind be already down, it will be moved up once a Weather Warning becomes active (see _Weather Control II_)\n - Turning Weather Control off will automatically also turn off Manual Override and disables the respective Dashboard button\n \n## Manual Override\n - Manual Override allows moving the Blind down although a Weather Warning is active\n - On a Manual Override a timer will be started that keeps the override condition active for a number of hours (can be configured in the _Flow Settings_)\n\n## Slider\n - The Slider is used to represent the current Level of the Blind. It is not used to actively control the Blind’s Level.\n - Slider to the left means Blind fully up (Level = 1)\n - Slider on the right means the Blind is fully down (Level = 0)\n - Slider control and HomeMatic Level definition is oppositional. The Set Direction node reverts the Level and makes sure the Slider is disabled.\n\n## Icons\n - Icons are used to visualize different conditions, e.g. the wind icon will turn red when a Wind Warning is active\n - Icons are controlled throughout the flow with the msg.icon property\n - The icons require a properly setup `httpStatic` directive in Node-Red's `settings.json` file \n",
        "x": 80,
        "y": 260,
        "wires": []
    },
    {
        "id": "4dbc2af7.381604",
        "type": "comment",
        "z": "dd34e985.e423c",
        "name": "Blind Control",
        "info": "\nFollowing Logic is applied:\n\n 1. Get the pressed button event. Following states are recognized\n\t - `PRESS_SHORT`: Move the Blind up or down to the endstops. Press again to stop\n\t - `PRESS_LONG` / `PRESS_LONG_RELEASE`: Start moving the Blind until the button is released\n\t - Dashboard buttons\n 2. Check if `WORKING` condition is true \n\t - If `false` start movement\n\t - If `true` stop movement\n 3. Set Dashboard icons and *Flow Setting* `Working` variable\n 4. Send commands to the actor\n 5. Before moving the Blind down, check weather conditions. See *Weather Control I* and *II*",
        "x": 90,
        "y": 920,
        "wires": []
    },
    {
        "id": "b7188462.e41fb",
        "type": "comment",
        "z": "dd34e985.e423c",
        "name": "Helper",
        "info": "## Level\n - Read the current `LEVEL` property from the Blind’s actor\n - Saves the Level in the Flow Settings\n - Set the Dashboard icons according to the Level\n\n## Working\n - Read the `WORKING` Property from the Blind’s actor\n - Saves the Working state in the Flow Settings. This might seem redundant but tests have shown that the Working feedback from the actor might be delayed a bit. It is faster to work with local variables \n",
        "x": 70,
        "y": 660,
        "wires": []
    },
    {
        "id": "de8dbdf5.fc5128",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "WeatherWarningIn",
        "links": [
            "9edfe11d.5315d",
            "e11c2008.0b986"
        ],
        "x": 235,
        "y": 1400,
        "wires": [
            [
                "695bc391.999c7c"
            ]
        ]
    },
    {
        "id": "32e1d555.602e42",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get Log 'logWeatherClose'",
        "rules": [
            {
                "t": "set",
                "p": "log",
                "pt": "msg",
                "to": "#:(file)::cBlindSettings.logMsg.logWeatherClose",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1410,
        "y": 1820,
        "wires": [
            [
                "ef2d71ee.8f9e48"
            ]
        ]
    },
    {
        "id": "ef2d71ee.8f9e48",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "logBlindOut",
        "links": [
            "eba490df.effd18"
        ],
        "x": 1575,
        "y": 1820,
        "wires": []
    },
    {
        "id": "151d51cb.c8cc2e",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WorkingOut",
        "links": [
            "909f447.037aa38",
            "e143eba9.bf57"
        ],
        "x": 575,
        "y": 840,
        "wires": []
    },
    {
        "id": "909f447.037aa38",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "WorkingIn",
        "links": [
            "151d51cb.c8cc2e",
            "a390270b.e43088",
            "2a3d2d1e.91c34a",
            "1523bde4.e5523a"
        ],
        "x": 715,
        "y": 960,
        "wires": [
            [
                "cf8309e1.715b8"
            ]
        ]
    },
    {
        "id": "e143eba9.bf57",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "WorkingIn",
        "links": [
            "151d51cb.c8cc2e",
            "a390270b.e43088",
            "2a3d2d1e.91c34a",
            "1523bde4.e5523a"
        ],
        "x": 715,
        "y": 1120,
        "wires": [
            [
                "b6a4d921.05318"
            ]
        ]
    },
    {
        "id": "74dd766d.793788",
        "type": "moment",
        "z": "dd34e985.e423c",
        "name": "Set Time Stamp",
        "topic": "",
        "input": "",
        "inputType": "date",
        "inTz": "Europe/Berlin",
        "adjAmount": 0,
        "adjType": "days",
        "adjDir": "add",
        "format": "DD.MM.YY HH:mm",
        "locale": "de",
        "output": "timestamp",
        "outputType": "msg",
        "outTz": "Europe/Berlin",
        "x": 300,
        "y": 540,
        "wires": [
            [
                "2eb519f5.1a971e"
            ]
        ]
    },
    {
        "id": "58a6e613.5325c8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get OverrideEnabled",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.var.OverrideEnabled",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "OverrideEnabled",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 580,
        "y": 40,
        "wires": [
            [
                "856ca3d4.e13b88"
            ]
        ]
    },
    {
        "id": "c1472560.7a13a",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get WeatherControlEnabled",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.var.WeatherControlEnabled",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "WeatherControlEnabled",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 120,
        "wires": [
            [
                "e0c612fd.09eeb8"
            ]
        ]
    },
    {
        "id": "6032a4cb.df3104",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Get Level",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.var.Level",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 620,
        "y": 80,
        "wires": [
            [
                "2e807ac9.8c1b9e"
            ]
        ]
    },
    {
        "id": "2e807ac9.8c1b9e",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "LevelOut1",
        "links": [
            "4afec909.f4cb7"
        ],
        "x": 735,
        "y": 80,
        "wires": []
    },
    {
        "id": "856ca3d4.e13b88",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "OverrideEnabledOut1",
        "links": [
            "2dd8e055.9ca9a8"
        ],
        "x": 735,
        "y": 40,
        "wires": []
    },
    {
        "id": "e0c612fd.09eeb8",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WeatherControlEnabledOut1",
        "links": [
            "cac5add6.9e9be"
        ],
        "x": 735,
        "y": 120,
        "wires": []
    },
    {
        "id": "2dd8e055.9ca9a8",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "OverrideEnabledIn1",
        "links": [
            "856ca3d4.e13b88"
        ],
        "x": 55,
        "y": 420,
        "wires": [
            [
                "fe404e80.1f00c8"
            ]
        ]
    },
    {
        "id": "cac5add6.9e9be",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "WeatherControlEnabledIn1",
        "links": [
            "e0c612fd.09eeb8"
        ],
        "x": 55,
        "y": 300,
        "wires": [
            [
                "181bfac7.847afd"
            ]
        ]
    },
    {
        "id": "4afec909.f4cb7",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "LevelIn1",
        "links": [
            "2e807ac9.8c1b9e"
        ],
        "x": 55,
        "y": 480,
        "wires": [
            [
                "bd516bec.f6705"
            ]
        ]
    },
    {
        "id": "6b18156c.be39fc",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Working",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "true",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Working",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1310,
        "y": 1000,
        "wires": [
            [
                "a390270b.e43088"
            ]
        ]
    },
    {
        "id": "a390270b.e43088",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WorkingOut",
        "links": [
            "909f447.037aa38",
            "e143eba9.bf57"
        ],
        "x": 1415,
        "y": 1000,
        "wires": []
    },
    {
        "id": "4c10f7bc.89d9b8",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Working",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "true",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Working",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1310,
        "y": 1160,
        "wires": [
            [
                "2a3d2d1e.91c34a"
            ]
        ]
    },
    {
        "id": "2a3d2d1e.91c34a",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WorkingOut",
        "links": [
            "909f447.037aa38",
            "e143eba9.bf57"
        ],
        "x": 1415,
        "y": 1160,
        "wires": []
    },
    {
        "id": "db0e2faf.98d8c8",
        "type": "link in",
        "z": "dd34e985.e423c",
        "name": "BlindStopIn",
        "links": [
            "b9b74823.1f0748",
            "fe902e77.360a18"
        ],
        "x": 875,
        "y": 1080,
        "wires": [
            [
                "2b6fe418.bbd064"
            ]
        ]
    },
    {
        "id": "fe902e77.360a18",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "BlindStopOut",
        "links": [
            "db0e2faf.98d8c8"
        ],
        "x": 875,
        "y": 960,
        "wires": []
    },
    {
        "id": "4be34500.0aa3e4",
        "type": "change",
        "z": "dd34e985.e423c",
        "name": "Set Working",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "false",
                "tot": "bool"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Working",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1310,
        "y": 1040,
        "wires": [
            [
                "1523bde4.e5523a"
            ]
        ]
    },
    {
        "id": "1523bde4.e5523a",
        "type": "link out",
        "z": "dd34e985.e423c",
        "name": "WorkingOut",
        "links": [
            "909f447.037aa38",
            "e143eba9.bf57"
        ],
        "x": 1415,
        "y": 1040,
        "wires": []
    },
    {
        "id": "ce845f38.3a33d8",
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
        "queueTimeout": "5000",
        "queuePause": "250",
        "contextStore": "default"
    },
    {
        "id": "7d72eec5.181528",
        "type": "ui_group",
        "z": "",
        "name": "Control2",
        "tab": "357e5891.192df",
        "order": 2,
        "disp": false,
        "width": "6",
        "collapse": false
    },
    {
        "id": "1168d453.1330dc",
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
        "id": "f07d5d3a.e76188",
        "type": "ui_group",
        "z": "",
        "name": "Weather",
        "tab": "357e5891.192df",
        "order": 3,
        "disp": false,
        "width": "3",
        "collapse": false
    },
    {
        "id": "964ef30e.7bd9c8",
        "type": "ui_group",
        "z": "",
        "name": "Log",
        "tab": "357e5891.192df",
        "order": 4,
        "disp": false,
        "width": "6",
        "collapse": false
    },
    {
        "id": "470ead96.ebe19c",
        "type": "ui_group",
        "z": "",
        "name": "Control1",
        "tab": "357e5891.192df",
        "order": 1,
        "disp": false,
        "width": "1",
        "collapse": false
    },
    {
        "id": "357e5891.192df",
        "type": "ui_tab",
        "z": "",
        "name": "Markise",
        "icon": "dashboard"
    }
]