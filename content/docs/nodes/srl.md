---
title: "SRLinux"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 2
---
# Overview
Node type `srl` represents node running Nokia SRLinux.


## Configuration 
see [KNL API - SRLinux]({{< relref "docs/api/#srlinux" >}})

## Example
```yaml
    nodes:
      srl-1:
        srl:
          chassis: ixr-h5-32d
          image: ghcr.io/nokia/srlinux:25.10
          memory: 4Gi
          cpu: 2.0

```

## Image
SRL node uses offical [Nokia SRLinux image](https://github.com/nokia/srlinux-container-image).

## Chassis
KNL supported following chassis models:
- ixr-d1     
- ixr-d2l    
- ixr-d3l    
- ixr-d5  
- ixr-h2     
- ixr-h3     
- ixr-6e  
- ixr-10e    
- ixr-18e    
- ixr-x1b    
- ixr-x3b    
- ixr-h5-32d 
- sxr-1d-32d 
- ixs-a1  

default model is `ixr-d3l`

> [!NOTE]
> **Note**  
> currently KNL only creates single card in a chassis, even in case of multi-slot chassis.

## SRL ethernet interface mapping
For a given SRL node, by default, the 1st ethernet interface (`ethernet-1/1`) is mapped to the 1st lab link it belongs to, 2nd interface (`ethernet-1/2`) is mapped to the 2nd lab link it belongs to ..so on. 
The first lab link is the first in lexicographical order of the link name.

In example below, `link2` is before `link9` in lexicographical order, so `ethernet-1/1` of `srl-1` connects to `pod-2`, `ethernet-1/2` connects to `pod-2`
```yaml
apiVersion: knl.kubenetlab.net/v1beta1
kind: Lab
metadata:
    name: srl
    namespace: knl-system
spec:
  links:
    link9:
      nodes:
      - node: srl-1
      - node: pod-1
    link2:
      nodes:
      - node: srl-1
      - node: pod-2
```
However if `port` is specified, then its value is used to mapping to the SRL interfaces, the format is `e1-X`, like `e1-1` means `ethernet-1/1`.
```yaml
apiVersion: knl.kubenetlab.net/v1beta1
kind: Lab
metadata:
    name: srl
    namespace: knl-system
spec:
  links:
    link9:
      nodes:
      - node: srl-1
        port: e1-3 # map to ethernet-1/3
      - node: pod-1
    link2:
      nodes:
      - node: srl-1
        port: e1-4 # map to ethernet-1/4
      - node: pod-2
```

## License 
License is required for multi-slot chassis models, specified via `license` field in the node configuration, the value is name of a k8s secrete in `knl-system` hold the license with key `license`, the default secret name is `srllic`.

## Persistent storage
On each SRLinux node, the files include startup `config.json` under `/etc/opt/srlinux/` are persistent on an auto created k8s PVC (size: 100MB) after lab is removed. 

## Memory & CPU 
Node configuraion also contains cpu and memory requests for the pod, which would be used as input for k8s to schedule the pod; minimal 4GB memory is required (which is also the default value), certain model requires more memory like 8GB. 
CPU request is optional.

> [!NOTE]
> **Note**  
> If the pod keep crashing/restarting, one possible reason is not having enough memory, increasing request memory in the node configuration could fix it.

