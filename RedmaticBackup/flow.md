[
    {
        "id": "6b062bbd.b16354",
        "type": "tab",
        "label": "Backup",
        "disabled": false,
        "info": ""
    },
    {
        "id": "b594b4f0.f645d",
        "type": "inject",
        "z": "6b062bbd.b16354",
        "name": "Init Settings",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "x": 150,
        "y": 120,
        "wires": [
            [
                "6aaac1df.d1d7b"
            ]
        ]
    },
    {
        "id": "6aaac1df.d1d7b",
        "type": "function",
        "z": "6b062bbd.b16354",
        "name": "Backup Settings",
        "func": "var backupSettings = {};\n\nbackupSettings = {\n  'pathBackup': '/media/usb0/redmatic/backup/',\n  'fileFlow': '/usr/local/addons/redmatic/var/flows.json',\n  'fileFlowCred': '/usr/local/addons/redmatic/var/flows_cred.json',\n  'keepBackups': 5,\n  'useEmail': true,\n  'usePushover': true,\n  'useDropbox': true,\n  'pushover': {\n    'device': 'bla',\n    'topicSuccess': 'Redmatic backup successfull',\n    'topicFailure': 'Redmatic backup failed',\n    'prioritySuccess': 0,\n    'priorityFailure': 2,\n    'soundSuccess': 'magic',\n    'soundFailure': 'spacealarm'\n  },\n  'email': {\n    'to': 'bla@bla.com',\n    'subjectSuccess': 'Redmatic backup successfull',\n    'subjectFailure': 'Redmatic backup failed',\n    'useAttachment': true\n  },\n  'temp': { //Filled during runtime\n    'lastTime': '',\n    'lastCommand': '',\n    'lastReturnCode': '',\n    'lastError': '',\n    'fullFile': ''\n  }\n};\n\nflow.set('backupSettings', backupSettings);\n\nreturn null;\n\n",
        "outputs": 1,
        "noerr": 0,
        "x": 330,
        "y": 120,
        "wires": [
            []
        ]
    },
    {
        "id": "59e108ba.6c95",
        "type": "inject",
        "z": "6b062bbd.b16354",
        "name": "Start Backup",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "00 02 * * *",
        "once": false,
        "onceDelay": 0.1,
        "x": 140,
        "y": 240,
        "wires": [
            [
                "bddb59c8.cfcd8"
            ]
        ]
    },
    {
        "id": "bddb59c8.cfcd8",
        "type": "function",
        "z": "6b062bbd.b16354",
        "name": "Build commands",
        "func": "var a = new Date(msg.payload);\nvar year = String(a.getFullYear());\nvar month = String(a.getMonth() + 1 < 10 ? '0' + a.getMonth() + 1 : a.getMonth() + 1);\nvar date = String(a.getDate() < 10 ? '0' + a.getDate() : a.getDate());\nvar hour = String(a.getHours() < 10 ? '0' + a.getHours() : a.getHours());\nvar min = String(a.getMinutes() < 10 ? '0' + a.getMinutes() : a.getMinutes());\nvar sec = String(a.getSeconds() < 10 ? '0' + a.getSeconds() : a.getSeconds());\nvar time = year + month + date + '_' + hour + '_' + min + '_' + sec;\nflow.set('backupSettings.temp.lastTime', a);\nflow.set('backupSettings.temp.fullFile', (flow.get('backupSettings').pathBackup + time + '_flow.tar.gz'));\n\n\n// Setup commands\n\n// Check if backup directory exists\n// If not, create it\nvar comCheckDir = 'if [ ! -d \"' + flow.get('backupSettings.pathBackup') +\n                  '\" ]; then mkdir ' + flow.get('backupSettings.pathBackup') +\n                  '; fi';\n\n// Tar.gz the flow.json and flow_cred.json\n// file into the backup directory\nvar comTar = 'tar -zcvf ' +\n             flow.get('backupSettings.temp.fullFile') + ' ' +\n             flow.get('backupSettings').fileFlow + ' ' +\n             flow.get('backupSettings').fileFlowCred;\n\n// Only keep x backups\n// Delete the excess ones according to their age\n// Explanation of the commands:\n// 1. cd --> Change directory to the backup dir\n// 2. ls -pt --> List dir content, mark folders with a /, sort time\n// 3. grep -v / --> Grep everything but /\n// 4. sed -e \"1,xd\" --> Get the first x results\n// 5. xargs -r rm --> Pass every result to rm\nvar comRotate = 'cd ' + flow.get('backupSettings.pathBackup') + ' && ' +\n                'ls -pt ' + flow.get('backupSettings.pathBackup') +\n                ' | grep -v / | sed -e \"1,' + flow.get('backupSettings.keepBackups') +\n                'd\" | xargs -r rm';\n\nmsg.queue = [comCheckDir, comTar, comRotate];\nreturn msg;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 330,
        "y": 240,
        "wires": [
            [
                "623a00db.81fba8"
            ]
        ]
    },
    {
        "id": "623a00db.81fba8",
        "type": "function",
        "z": "6b062bbd.b16354",
        "name": "Manage Command Queue",
        "func": "var commandOK,\n    msg2 = {},\n    commandQueue = context.get('commandQueue') || [];\n\n// Queued command are expected as msg.queue\n// With receiving a new queue start with\n// commandOK = 0\nif (msg.hasOwnProperty('queue')) {\n  commandQueue = msg.queue;\n  context.set('commandQueue', commandQueue);\n  commandOK = 0;\n}\n\n// Check if an incoming message has the 'rc' property\n// and set commandOK to the return code of the\n// exec node\nif (msg.hasOwnProperty('rc')) {\n  commandOK = msg.rc.code;\n  flow.set('backupSettings.temp.lastReturnCode', commandOK);\n  flow.set('backupSettings.temp.lastError', msg.rc.message);\n}\n\n// If the return code is not 0 then issue error\n// message and clear the command queue\n// Send error message on 2nd output\nif (commandOK !== 0) {\n  node.warn(msg.rc.message);\n  msg2.payload = msg.rc.message;\n  msg2.success = false;\n  context.set('commandQueue', []);\n  node.status({fill:'red', shape:'dot', text:'failure'});\n  if (flow.get('backupSettings.useEmail')) {\n    msg2.useEmail = true;\n  }\n  if (flow.get('backupSettings.usePushover')) {\n    msg2.usePushover = true;\n  }\n  return [null, msg2];\n}\n\n// If the command queue is empty and last return code\n// indicated success, then issue a completed message\nif (commandOK === 0 && commandQueue.length === 0) {\n  msg2.payload = 'All commands executed successfully';\n  msg2.success = true;\n  node.status({fill:'green', shape:'dot', text:'success'});\n  if (flow.get('backupSettings.useEmail')) {\n    msg2.useEmail = true;\n  }\n  if (flow.get('backupSettings.useEmail') && flow.get('backupSettings.email.useAttachment')) {\n    msg2.useAttachment = true;\n    msg2.filename = flow.get('backupSettings.temp.fullFile');\n  }\n  if (flow.get('backupSettings.usePushover')) {\n    msg2.usePushover = true;\n  }\n  if (flow.get('backupSettings.useDropbox')) {\n    msg2.useDropbox = true;\n    msg2.localFilename = flow.get('backupSettings.temp.fullFile');\n  }\n  return [null, msg2];\n}\n\n// If there are commands in the command queue and\n// the last return code is 0 (success) then send\n// next command to the exec node\nif (commandQueue.length > 0 && commandOK === 0) {\n  var commandNext = commandQueue.shift();\n  msg2.payload = commandNext;\n  context.set('commandQueue', commandQueue);\n  flow.set('backupSettings.temp.lastCommand', commandNext);\n  return [msg2, null];\n}\n",
        "outputs": 2,
        "noerr": 0,
        "x": 580,
        "y": 240,
        "wires": [
            [
                "3d1bfe32.c4db2a"
            ],
            [
                "ec9d5810.b2e18"
            ]
        ],
        "outputLabels": [
            "command",
            "error"
        ]
    },
    {
        "id": "3d1bfe32.c4db2a",
        "type": "exec",
        "z": "6b062bbd.b16354",
        "command": "",
        "addpay": true,
        "append": "",
        "useSpawn": "false",
        "timer": "",
        "oldrc": false,
        "name": "Execute Command Queue",
        "x": 580,
        "y": 160,
        "wires": [
            [
                "623a00db.81fba8"
            ],
            [],
            []
        ]
    },
    {
        "id": "2e68e668.905e8a",
        "type": "e-mail",
        "z": "6b062bbd.b16354",
        "server": "smtp.gmail.com",
        "port": "465",
        "secure": true,
        "tls": true,
        "name": "",
        "dname": "",
        "x": 930,
        "y": 360,
        "wires": []
    },
    {
        "id": "30c77642.195cd2",
        "type": "switch",
        "z": "6b062bbd.b16354",
        "name": "useEmail",
        "property": "useEmail",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 180,
        "y": 420,
        "wires": [
            [
                "2421e3e8.942764"
            ]
        ]
    },
    {
        "id": "2421e3e8.942764",
        "type": "switch",
        "z": "6b062bbd.b16354",
        "name": "useAttachment",
        "property": "useAttachment",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            },
            {
                "t": "else"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 380,
        "y": 420,
        "wires": [
            [
                "e8cfda17.5dc65"
            ],
            [
                "15b580d0.01259f"
            ]
        ]
    },
    {
        "id": "ef180d5a.43081",
        "type": "switch",
        "z": "6b062bbd.b16354",
        "name": "useDropbox",
        "property": "useDropbox",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 170,
        "y": 600,
        "wires": [
            [
                "23a41b3a.08e61c"
            ]
        ]
    },
    {
        "id": "d552f149.03256",
        "type": "switch",
        "z": "6b062bbd.b16354",
        "name": "usePushover",
        "property": "useEmail",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 170,
        "y": 640,
        "wires": [
            [
                "d9178f95.92e528"
            ]
        ]
    },
    {
        "id": "23a41b3a.08e61c",
        "type": "dropbox out",
        "z": "6b062bbd.b16354",
        "dropbox": "",
        "filename": "",
        "localFilename": "",
        "name": "",
        "x": 400,
        "y": 600,
        "wires": []
    },
    {
        "id": "2a382a27.e68bae",
        "type": "pushover",
        "z": "6b062bbd.b16354",
        "name": "",
        "device": "",
        "title": "",
        "priority": 0,
        "sound": "",
        "url": "",
        "url_title": "",
        "html": false,
        "x": 760,
        "y": 660,
        "wires": []
    },
    {
        "id": "d9178f95.92e528",
        "type": "switch",
        "z": "6b062bbd.b16354",
        "name": "Switch Success",
        "property": "success",
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
        "x": 380,
        "y": 640,
        "wires": [
            [
                "bfe2d769.876a48"
            ],
            [
                "cbe2543b.01101"
            ]
        ]
    },
    {
        "id": "bfe2d769.876a48",
        "type": "change",
        "z": "6b062bbd.b16354",
        "name": "Success",
        "rules": [
            {
                "t": "set",
                "p": "device",
                "pt": "msg",
                "to": "backupSettings.pushover.device",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "backupSettings.pushover.topicSuccess",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "priority",
                "pt": "msg",
                "to": "backupSettings.pushover.prioritySuccess",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "sound",
                "pt": "msg",
                "to": "backupSettings.pushover.soundSuccess",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 600,
        "y": 634,
        "wires": [
            [
                "2a382a27.e68bae"
            ]
        ]
    },
    {
        "id": "cbe2543b.01101",
        "type": "change",
        "z": "6b062bbd.b16354",
        "name": "Failure",
        "rules": [
            {
                "t": "set",
                "p": "device",
                "pt": "msg",
                "to": "backupSettings.pushover.device",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "backupSettings.pushover.topicFailure",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "priority",
                "pt": "msg",
                "to": "backupSettings.pushover.priorityFailure",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "sound",
                "pt": "msg",
                "to": "backupSettings.pushover.soundFailure",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 610,
        "y": 680,
        "wires": [
            [
                "2a382a27.e68bae"
            ]
        ]
    },
    {
        "id": "e8cfda17.5dc65",
        "type": "change",
        "z": "6b062bbd.b16354",
        "name": "Email Properties",
        "rules": [
            {
                "t": "set",
                "p": "attachments",
                "pt": "msg",
                "to": "[{'path': $$.filename}]",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "to",
                "pt": "msg",
                "to": "backupSettings.email.to",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "backupSettings.email.subjectSuccess",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 380,
        "y": 360,
        "wires": [
            [
                "2e68e668.905e8a"
            ]
        ]
    },
    {
        "id": "ca859173.2c2028",
        "type": "change",
        "z": "6b062bbd.b16354",
        "name": "Success",
        "rules": [
            {
                "t": "set",
                "p": "device",
                "pt": "msg",
                "to": "backupSettings.pushover.device",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "backupSettings.pushover.topicSuccess",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "priority",
                "pt": "msg",
                "to": "backupSettings.pushover.prioritySuccess",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "sound",
                "pt": "msg",
                "to": "backupSettings.pushover.soundSuccess",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 480,
        "wires": [
            [
                "470e3724.9acda8"
            ]
        ]
    },
    {
        "id": "93e1286f.ddbb8",
        "type": "change",
        "z": "6b062bbd.b16354",
        "name": "Failure",
        "rules": [
            {
                "t": "set",
                "p": "device",
                "pt": "msg",
                "to": "backupSettings.pushover.device",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "backupSettings.pushover.topicFailure",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "priority",
                "pt": "msg",
                "to": "backupSettings.pushover.priorityFailure",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "sound",
                "pt": "msg",
                "to": "backupSettings.pushover.soundFailure",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 570,
        "y": 520,
        "wires": [
            [
                "5b027f45.c7a68"
            ]
        ]
    },
    {
        "id": "470e3724.9acda8",
        "type": "change",
        "z": "6b062bbd.b16354",
        "name": "Email Properties",
        "rules": [
            {
                "t": "set",
                "p": "to",
                "pt": "msg",
                "to": "backupSettings.email.to",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "backupSettings.email.subjectSuccess",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 740,
        "y": 480,
        "wires": [
            [
                "2e68e668.905e8a"
            ]
        ]
    },
    {
        "id": "5b027f45.c7a68",
        "type": "change",
        "z": "6b062bbd.b16354",
        "name": "Email Properties",
        "rules": [
            {
                "t": "set",
                "p": "to",
                "pt": "msg",
                "to": "backupSettings.email.to",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "backupSettings.email.subjectFailure",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 740,
        "y": 520,
        "wires": [
            [
                "2e68e668.905e8a"
            ]
        ]
    },
    {
        "id": "15b580d0.01259f",
        "type": "switch",
        "z": "6b062bbd.b16354",
        "name": "Switch Success",
        "property": "success",
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
        "x": 380,
        "y": 480,
        "wires": [
            [
                "ca859173.2c2028"
            ],
            [
                "93e1286f.ddbb8"
            ]
        ]
    },
    {
        "id": "d25ce8b3.67c83",
        "type": "link in",
        "z": "6b062bbd.b16354",
        "name": "backupStatus",
        "links": [
            "ec9d5810.b2e18"
        ],
        "x": 55,
        "y": 420,
        "wires": [
            [
                "30c77642.195cd2"
            ]
        ]
    },
    {
        "id": "ec9d5810.b2e18",
        "type": "link out",
        "z": "6b062bbd.b16354",
        "name": "backupStatus",
        "links": [
            "21763c30.5f6d34",
            "7c66114a.972b98",
            "d25ce8b3.67c83"
        ],
        "x": 755,
        "y": 247,
        "wires": []
    },
    {
        "id": "7c66114a.972b98",
        "type": "link in",
        "z": "6b062bbd.b16354",
        "name": "backupStatus",
        "links": [
            "ec9d5810.b2e18"
        ],
        "x": 55,
        "y": 600,
        "wires": [
            [
                "ef180d5a.43081"
            ]
        ]
    },
    {
        "id": "21763c30.5f6d34",
        "type": "link in",
        "z": "6b062bbd.b16354",
        "name": "backupStatus",
        "links": [
            "ec9d5810.b2e18"
        ],
        "x": 55,
        "y": 640,
        "wires": [
            [
                "d552f149.03256"
            ]
        ]
    },
    {
        "id": "e0fdd6b7.c1618",
        "type": "comment",
        "z": "6b062bbd.b16354",
        "name": "Backup Settings",
        "info": "",
        "x": 140,
        "y": 80,
        "wires": []
    },
    {
        "id": "a6b3ee04.b3f97",
        "type": "comment",
        "z": "6b062bbd.b16354",
        "name": "Start Backup and Command Management",
        "info": "",
        "x": 220,
        "y": 200,
        "wires": []
    },
    {
        "id": "d8b06c00.60c1c8",
        "type": "comment",
        "z": "6b062bbd.b16354",
        "name": "Notifications ",
        "info": "",
        "x": 130,
        "y": 320,
        "wires": []
    }
]