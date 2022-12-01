---
title: Kytos metadata
parent: NApps
---
Kytos's reserved metadata
=========================

Overview
--------

Kytos provides a way to add extra information to a
napp and other Kytos objects. From a napp developer
perspective, the napp has to provide APIs to store,
retrieve and manage Metadata information. From the
Kytos core perspective objects like Switch,
Interface and Link have their metadata managed
by the Topology Kytos Napp. In addition to this, 
sometimes, metadata needs to be used internally and exclusively, 
meaning that writes and deletions should only be done by the NApp that
owns this resource, and shouldn't be allowed via APIs.

From a user perspective, you have to make sure
you are using the proper metadata and also
make a fair usage of it, which means:

- check if the metadata you want is not already
there before creating a new one
- follow the metadata specification regarding data
type and semantics
- use a meaningful and expressive metadata name
- before starting using new metadata, submit a PR
for documenting the metadata on this page

Metadata
--------

| Metadata            | Napp        | Object     | Type  | Description                                   | Date       | Used by                       |
|---------------------|-------------|------------|-------|-----------------------------------------------|------------|-------------------------------|
| availability        | topology    | link       | float | Link's availability in %                      | 10.15.2021 | sdx napp                      |
| link\_name          | topology    | link       | str   | Link's Name                                   | 10.15.2021 | sdx napp, evc\_manager        |
| packet\_loss        | topology    | link       | float | Link's packet loss in %                       | 10.15.2021 | sdx napp                      |
| bandwidth           | topology    | link       | int   | Link's bandwidth in Gbps                      | 10.15.2021 | sdx napp, pathfinder          |
| address             | topology    | switch     | str   | Node's physical address                       | 10.15.2021 | sdx napp                      |
| lat                 | topology    | switch     | str   | Node's latitude                               | 10.15.2021 | sdx napp, kytos UI            |
| lng                 | topology    | switch     | str   | Node's longitude                              | 10.15.2021 | sdx napp, kytos UI            |
| node\_name          | topology    | switch     | str   | Node's name                                   | 10.15.2021 | sdx napp, evc\_manager        |
| mtu                 | topology    | interfaces | int   | Interface's mtu in bytes                      | 10.15.2021 | sdx napp                      |
| port\_name          | topology    | interfaces | str   | Interface's name                              | 10.15.2021 | sdx napp, evc\_manager        |
| int                 | mef\_eline  | evc        | str   | If INT is enabled for the EVC                 | Planned    | int napp (prototype phase)    |
| mirror              | mef\_eline  | evc        | str   | If Mirror is enabled for the EVC              | Planned    | mirror napp (prototype phase) |
| metrics             | mef\_eline  | evc        | dict  | EVC's user-requested metrics                  | Planned    | mef\_eline                    |
| monitoring          | mef\_eline  | evc        | dict  | EVC's monitoring approaches                   | Planned    | monitoring napp (planned)     |
| notifications       | mef\_eline  | evc        | dict  | EVC's notification                            | Planned    | monitoring napp (planned)     |
| frr                 | mef\_eline  | evc        | dict  | If Fast ReRoute is enabled for the EVC        | Planned    | frr napp (planned)            |
| ownership           | topology    | link       | dict  | Link's owners                                 | 02.02.2022 | pathfinder                    |
| utilization         | topology    | link       | float | Link's average utilization in %               | 02.02.2022 | pathfinder                    |
| reliability         | topology    | link       | float | Link's reliability in %                       | 02.02.2022 | pathfinder                    |
| priority            | topology    | link       | float | Link's priority, used for traffic engineering | 02.02.2022 | pathfinder                    |
| delay               | topology    | link       | float | Link's latency in ms                          | 02.02.2022 | pathfinder                    |


Internal Metadata
-----------------


| Metadata              | Napp        | Object     | Type      | Description                                   | Date       | Used by                       |
|-----------------------|-------------|------------|-----------|-----------------------------------------------|------------|-------------------------------|
| liveness_status       | topology    | link       | str       | Link's liveness status  up\|down              | 06.30.22   | topology, core                |
| last_status_change    | topology    | link       | float     | Link's last status change timestamp           | 06.30.22   | topology                      |
| last_status_is_active | topology    | link       | bool      | Whether Link's last status is active or not   | 06.30.22   | topology                      |
| notified_up_at        | topology    | link       | datetime  | Link's last notified up datetime              | 11.18.22   | topology                      |


Usage examples
--------------

Retrieving switch's name:
```
curl -s http://127.0.0.1:8181/api/kytos/topology/v3/switches/00:00:00:00:00:00:00:01/metadata | jq -r '.metadata.node_name'
```

Update Link's name
```
curl -s -X POST -H 'Content-type: application/json' http://127.0.0.1:8181/api/kytos/topology/v3/links/4d42dc0852278accac7d9df15418f6d921db160b13d674029a87cef1b5f67f30/metadata -d '{"link_name": "MIA-FLZ-100G"}'
```

Update switch's latitude and longitude:
```
curl -s -X POST -H 'Content-type: application/json' http://127.0.0.1:8181/api/kytos/topology/v3/switches/00:00:00:00:00:00:00:01/metadata -d '{"lat": "25.7553646","lng": "-80.3760393"}'
```
