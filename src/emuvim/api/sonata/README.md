# SONATA dummy gatekeeper API:

## Requirements

* uuid
* hashlib
* zipfile
* yaml
* docker-py
* flask
* flask_restful

## Run REST API in standalone mode (without emulator):
* `python src/emuvim/api/sonata/dummygatekeeper.py`

## Run emulator example with active SONATA dummy gatekeeper:
The example starts a small network with two data centers.

* `sudo python src/emuvim/examples/sonata_y1_demo_topology_1.py`

## Upload a package (*.son) file:

To upload the file `sonata-demo-docker.son` (can be found in `son-emu/misc/`) do:

* `curl -i -X POST -F package=@sonata-demo-docker.son http://127.0.0.1:5000/packages`

To list all uploaded packages do:

* `curl http://127.0.0.1:5000/packages`

To instantiate (start) a service do:

* Specific service: `curl -X POST http://127.0.0.1:5000/instantiations -d "{\"service_uuid\":\"59446b64-f941-40a8-b511-effb0512c21b\"}"`
* Last uploaded service (makes manual tests easier): `curl -X POST http://127.0.0.1:5000/instantiations -d "{}"`

To list all running services do:

* `curl http://127.0.0.1:5000/instantiations`

## VNF Requirements

A VNF container that is used with the dummy gatekeeper should specify a emulator specific entrypoint script in its Dockerfile:

* `ENV SON_EMU_CMD ./start.sh`

This script is called by the dummy gatekeeper after a VNF was started and the chaining was done. The script can then be used to trigger the execution of individual VNF software.

ATTENTION: The specified script must not block!

## API definition

This page describes the dummy gatekeeper API. This gatekeeper fakes the original platform gatekeeper during development SDK tools like son-push. 

It is also able to deploy our example service package (not any arbitrary service package!) in the emulator for the Y1 demo.

_Note: This API should converge to the API of the original GK as much as possible!_

## REST API:
<table>
<tr>
<th>Endpoint:</th>
<th>Method:</th>
<th>Header:</th>
<th>Body:</th>
<th>Response:</th>
</tr>
<tr>
<td>/packages</td>
<td>POST</td>
<td>-</td>
<td>{file-content} as "content-type": "multipart/form-data"</td>
<td>{"service_uuid": "c880aaab-f3b9-43ac-ac6b-3d27b46146b7", size=456, sha1=49ee6468dfa4ecbad440d669b249d523a38651be, error: null}</td>
</tr>
<tr>
<td>/packages</td>
<td>GET</td>
<td>-</td>
<td></td>
<td>{service_uuid_list: ["c880aaab-f3b9-43ac-ac6b-3d27b46146b7", "c880aaab-f3b9-43ac-ac6b-3d27b46146b8", "c880aaab-f3b9-43ac-ac6b-3d27b46146b9"]}</td>
</tr>
<tr>
<td>/instantiations</td>
<td>POST</td>
<td>-</td>
<td>{service_uuid: "c880aaab-f3b9-43ac-ac6b-3d27b46146b7"}</td>
<td>{service_instance_uuid: "de4567-f3b9-43ac-ac6b-3d27b461123"}</td>
</tr>
<tr>
<td>/instantiations</td>
<td>GET</td>
<td>-</td>
<td></td>
<td>
{
    "service_instance_list": [
        [
            "9da044b3-1f7a-40e6-a9b3-9e83a9834249", 
            "9371df14-a595-436a-92b5-fc243b74a9d7"
        ]
    ]
}
</td>
</tr>
</table>





## Cleanup
The dummy GK will store some files in your tmp folder in order to fake "catalogues" and "repositories" etc.

* ` sudo rm -r /tmp/son-dummy-gk/`