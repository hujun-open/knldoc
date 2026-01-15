---
title: "SR-SIM"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 3
---
# Overview
Node type `srsim` represents Nokia SR-SIM, running SR OS. 

## Configuration
see [KNL API - SRSim]({{< relref "docs/api/#srsim" >}})

## Image
The image is the Nokia published SR-SIM container image, currently it is only available in the format of image archive, so it needs to be uploaded to a registry before it could be used by KNL. 

## Chassis configuration
SR-SIM chassis uses same struct as [vSIM]({{< relref "docs/nodes/vsim/#chassis-configuration">}})

For supported chassis/card/mda/xiom, refer to `Appendix A` in `Nokia SR-SIM Installation, Setup, and Deployment Guide` of corresponding SROS release; here is a [link to SR OS 25.10.R1](https://documentation.nokia.com/sr/25-10/7x50-shared/srsim-installation-setup/appendices.html#ai89jtwgeh)

## MDA port mapping
By default, the SR-SIM port mapping is same as default port mapping of [vSIM]({{< relref "docs/nodes/vsim/#mda-port-mapping">}});

The `port` field in the `Connector` of a lab link could used to specify MDA port id, its format follows same rule described in `Datapath interfaces` chapter in `Nokia SR-SIM Installation, Setup, and Deployment Guide` of corresponding SROS release; here is a [link to SR OS 25.10.R1](https://documentation.nokia.com/sr/25-10/7x50-shared/srsim-installation-setup/data-path-interfaces.html); e.g. `e1-2-3`

## Persistent storage
`CF1`, `CF2` and `CF3` are backed by dedicate PVCs, so all files on `CFx` cards are pesistent even in case lab is removed.

## License
SR-SIM requires a license to run, specified by `license` field in the node configuration, the value is name of a k8s secrete in `knl-system` hold the license with key `license`, the default secret name is `srsimlic`.