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
        "func": "var backupSettings = {};\n\nbackupSettings = {\n  'pathBackup': '/media/usb0/redmatic/backup/',\n  'fileFlow': '/usr/local/addons/redmatic/var/flows.json',\n  'fileFlowCred': '/usr/local/addons/redmatic/var/flows_cred.json',\n  'keepBackups': 5\n};\n\nflow.set('backupSettings', backupSettings);\n\nreturn null;",
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
        "y": 200,
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
        "func": "var a = new Date(msg.payload);\nvar year = String(a.getFullYear());\nvar month = String(a.getMonth() + 1 < 10 ? '0' + a.getMonth() + 1 : a.getMonth() + 1);\nvar date = String(a.getDate() < 10 ? '0' + a.getDate() : a.getDate());\nvar hour = String(a.getHours() < 10 ? '0' + a.getHours() : a.getHours());\nvar min = String(a.getMinutes() < 10 ? '0' + a.getMinutes() : a.getMinutes());\nvar sec = String(a.getSeconds() < 10 ? '0' + a.getSeconds() : a.getSeconds());\nvar time = year + month + date + '_' + hour + '_' + min + '_' + sec;\n\n\n// Setup commands\n\n// Check if backup directory exists\n// If not, create it\nvar comCheckDir = 'if [ ! -d \"' + flow.get('backupSettings').pathBackup +\n                  '\" ]; then mkdir ' + flow.get('backupSettings').pathBackup +\n                  '; fi';\n\n// Tar.gz the flow.json and flow_cred.json\n// file into the backup directory\nvar comTar = 'tar -zcvf ' +\n             flow.get('backupSettings').pathBackup + time + '_flow.tar.gz ' +\n             flow.get('backupSettings').fileFlow + ' ' +\n             flow.get('backupSettings').fileFlowCred;\n\n// Only keep x backups\n// Delete the excess ones according to their age\n// Explanation of the commands:\n// 1. cd --> Change directory to the backup dir\n// 2. ls -pt --> List dir content, mark folders with a /, sort time\n// 3. grep -v / --> Grep everything but /\n// 4. sed -e \"1,xd\" --> Get the first x results\n// 5. xargs -r rm --> Pass every result to rm\nvar comRotate = 'cd ' + flow.get('backupSettings').pathBackup + ' && ' +\n                'ls -pt ' + flow.get('backupSettings').pathBackup +\n                ' | grep -v / | sed -e \"1,' + flow.get('backupSettings').keepBackups +\n                'd\" | xargs -r rm';\n\nmsg.queue = [comCheckDir, comTar, comRotate];\nreturn msg;\n",
        "outputs": 1,
        "noerr": 0,
        "x": 330,
        "y": 200,
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
        "func": "var commandOK,\n    msg2 = {},\n    commandQueue = context.get('commandQueue') || [];\n\n// Queued command are expected as msg.queue\n// With receiving a new queue start with\n// commandOK = 0\nif (msg.hasOwnProperty('queue')) {\n  commandQueue = msg.queue;\n  context.set('commandQueue', commandQueue);\n  commandOK = 0;\n}\n\n// Check if an incoming message has the 'rc' property\n// and set commandOK to the return code of the\n// exec node\nif (msg.hasOwnProperty('rc')) {\n  commandOK = msg.rc.code;\n}\n\n// If the return code is not 0 then issue error\n// message and clear the command queue\n// Send error message on 2nd output\nif (commandOK !== 0) {\n  node.warn(msg.rc.message);\n  msg2.payload = msg.rc.message;\n  context.set('commandQueue', []);\n  return [null, msg2];\n}\n\n// If there are commands in the command queue and\n// the last return code is 0 (success) then send\n// next command to the exec node\nif (commandQueue.length > 0 && commandOK === 0) {\n  var commandNext = commandQueue.shift();\n  msg2.payload = commandNext;\n  context.set('commandQueue', commandQueue);\n  return [msg2, null];\n}\n",
        "outputs": 2,
        "noerr": 0,
        "x": 580,
        "y": 200,
        "wires": [
            [
                "3d1bfe32.c4db2a"
            ],
            [
                "63b9421a.f5cffc"
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
        "y": 280,
        "wires": [
            [
                "623a00db.81fba8"
            ],
            [],
            []
        ]
    },
    {
        "id": "63b9421a.f5cffc",
        "type": "debug",
        "z": "6b062bbd.b16354",
        "name": "process potential error messages",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "x": 900,
        "y": 207,
        "wires": []
    }
]