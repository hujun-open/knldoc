---
title: "General VM"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 99
---
# Overview
Node type `vm` represents a general VM, each `vm` node is a kubevirt VM instance.

## Configuration 
see [KNL API - General VM]({{< relref "docs/api/#generalvm" >}})


## Image
There are two support image access methods:
1. a  [Kubevirt container disk image](https://Kubevirt.io/user-guide/storage/disks_and_volumes/#containerdisk-workflow-example), with `docker://` prefix, like `docker://example.registry/debian:v13`
2. a HTTP(s) URL to a qcow2 disk image with "http://" or "https://" prefix.

## Example
Below example shows a lab with a `vm-debian` node connects to a pod, `vm-debian` uses a HTTPs URL of offical debian cloud image.
```yaml
apiVersion: knl.kubenetlab.net/v1beta1
kind: Lab
metadata:
  name: vm-example
  namespace: knl-system
spec:
  links:
    link-1:
      nodes:
      - node: vm-debian
      - node: pod-1
  nodes:
    vm-debian:
      vm:
        image: https://cloud.debian.org/images/cloud/trixie/latest/debian-13-genericcloud-amd64.qcow2
        diskSize: 10G

```

## Persistent storage
Each VM node has a kubevirt data volumn based its image, so all files inside the VM are persistent even in case lab is removed. the VM disk size could be specified via `diskSize` in the node spec.

## Initialization method
KNL supports two VM initialzation methods, which could specified by `init` field in the node spec:
1. cloud-init (default)
2. ignition 

Default username and password of the VM could be specified via `user` and `passwd` field (default value is `lab` and `lab123`) which is installed via corresponding initiazation method.

## Interface MAC, IP address and Routes
Optionally, a list of IP address could be assigned to the link interface via `addrs` field in the link spec; and also optionally a list of static routes could be specified using the interface as the egress, via `routes` field in format as `<prefix> via <nexthop>`;

The interface's MAC address could be optionally specified via `mac` field in the link spec, an auto generated value is used by default. 

These provisioned via the specified initialzation method.

Following is an example:
```yaml
    links:
        link1: 
          nodes:
          - node: vm-1
            addrs: [192.168.10.100/24, 2001:beef::100/64]
            routes: ["192.168.30.0/24 via 192.168.10.200"]
            mac: 32:20:22:99:99:01
          - node: pod-1
```


## Listening ports
`ports` field in the node spec specifies a list of VM's listening port on the default pod interface (the 1st interface). by default it only includes TCP port 22.

## CPU & Memory
CPU and memory of the VM could be specified in the node spec, by default cpu is 2 and memory is 4GB, and optionally CPU pinning and hugepage memory (1G page size) could be enabled.


