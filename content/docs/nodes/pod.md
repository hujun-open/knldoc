---
title: "General Pod"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 200
---
# Overview
Node type `pod` represents a general k8s Pod.

## Configuration 
see [KNL API - General VM]({{< relref "docs/api/#generalpod" >}})


## Example
Below example shows a lab with two pod node using differnet image and command, also assign an ip address to the connecting interface for `link-1`
```yaml
apiVersion: knl.kubenetlab.net/v1beta1
kind: Lab
metadata:
  name: pod-example
  namespace: knl-system
spec:
  links:
    link-1:
      nodes:
      - node: pod-1
        addrs: [192.168.1.100/24]
      - node: pod-2
        addrs: [192.168.1.200/24]
  nodes:
    pod-1:
      pod:
        image: busybox
        cmd: sh -c "sleep 10000"
    pod-2:
      pod:
        image: alpine
        cmd: sh -c "sleep 10000"

```

## Persistent storage
Each pod node has a PVC mounted on `/root`, the size could be specified via `pvcSize`, default value is `100M`.


## Interface IP address and Routes
Optionally, a list of IP address could be assigned to the link interface via `addrs` field in the link spec; and also optionally a list of static routes could be specified using the interface as the egress, via `routes` field in format as `<prefix> via <nexthop>`;

## CPU & Memory
CPU and memory of the pod could be specified in the node spec, by default there is no cpu and memory request.

