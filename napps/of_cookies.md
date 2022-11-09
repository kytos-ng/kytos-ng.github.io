---
title: OpenFlow cookie prefixes
parent: NApps
---

OpenFlow cookie prefixes
========================

Each NApp that sends FlowMod is encouraged to have a configurable and reserved 1-byte cookie prefix to identify which NApp owns a particular flow, to also facilitate queries, debugging and general troubleshooting. This byte is set as the most significant byte.

Currently, the following OpenFlow cookie prefixes have been reserved:

| **NApp**                                           | **OpenFlow cookie prefix / 56** |
|----------------------------------------------------|---------------------------------|
| [telemetry](https://github.com/kytos-ng/telemetry) | 0xA8                            |
| [mef_eline](https://github.com/kytos-ng/mef_eline) | 0xAA                            |
| [of_lldp](https://github.com/kytos-ng/of_lldp)     | 0xAB                            |
| [coloring](https://github.com/kytos-ng/coloring)   | 0xAC                            |
