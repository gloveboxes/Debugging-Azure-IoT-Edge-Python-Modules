# Debugging Python Azure IoT Edge Modules

## Opening a Python debug port in the docker container

Example of opening port 3000.

```json
{
    "HostConfig": {
        "PortBindings": {
            "3000/tcp": [
                {
                    "HostPort": "3000"
                }
            ]
        }
    }
}
```

The json needs to be minified and escaped. From Visual Studio Code install the following extensions

1. JSON Tools
2. JSON Escaper

Example

```json
{\"HostConfig\":{\"PortBindings\":{\"3000/tcp\":[{\"HostPort\":\"3000\"}]}}}
```

So the createOptions in the 'deployment.template.json' file for a module could look like the following.

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\": {\"3000/tcp\": [{\"HostPort\": \"3000\"}]}}}"
```

## Sync Python Source in Visual Studio Code for full debug experience

The IoT Edge project is multi rooted. You need to modify the project launcher.json file and extent the "localRoot" property to include the path to your Python IoT Edge Module.

```json
"version": "0.2.0",
    "configurations": [
        {
            "name": "Your IoT Edge Python Module: Attach",
            "type": "python",
            "request": "attach",
            "localRoot": "${workspaceFolder}/modules/yourmodule/app/}",
            "remoteRoot": "/app/",
            "port": 3000,
            "secret": "your secret",
            "host": "127.0.0.1"
        },
```

## Enable your Python Project for Remote Debgging

In your python project you need do do the following

1. Add ptvsd==3.0.0 to the requirments.txt document in the module to PIP install the remote debug library
2. add import ptvsd to your python script
3. Enable remote debug attach - ptvsd.enable_attach("your secret", address=('0.0.0.0', 3000))
4. If you want your code to halt until the debugger is atatched then add - ptvsd.wait_for_attach()

Example

```python
import os
import random
import sys
import time
import requests
import json
import ptvsd
import HubManager

# import iothub_client
# from iothub_client import (IoTHubClient, IoTHubClientError, IoTHubError,
#                            IoTHubMessage, IoTHubMessageDispositionResult,
#                            IoTHubTransportProvider)
from iothub_client import (IoTHubError, IoTHubMessage, IoTHubTransportProvider)

ptvsd.enable_attach("your secret", address=('0.0.0.0', 3000))
ptvsd.wait_for_attach()
```