---
title: "Requirements"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 1
---
# Overview
**Following requirements must be met before installing KNL.**

## k8s cluster
KNL should work with all types of k8s cluster: bare-metal, VM or container based. the compute resource (cpu, memory and storage) requirement mostly depends on overall requirement of lab nodes running, these resource requirement could be specified in the `Lab` CR. 

Certain KNL node types like `vsr`, `magc` requires CPU pinning and hugepage memory to performan optimally, which requires certain linux kernel settings on the k8s worker node. consult correspoding product documentation for details. 

> [!NOTE]
> **Note 0**  
> Currently KNL only support Linux/x86-64 k8s worker

> [!NOTE]
> **Note 1**  
> Admin access to the cluster is required to install KNL

> [!NOTE]
> **Note 2**  
> In case of VM based cluster (VM as k8s worker or container as worker inside VM), to support VM type node with decent performance, nested virtualization must be enabled on each VM. see instruction [here](https://docs.fedoraproject.org/en-US/quick-docs/using-nested-virtualization-in-kvm/index.html)

> [!NOTE]
> **Note 3**  
> In case of container based cluster (like kind), to support macvtap CNI plugin that KNL/k8slan uses, host `/dev` moust be mounted inside worker container



## registry
Varies KNL node types require container image to run, and some of them like Nokia vSIM don't have offical registry, it is important to have access to a image registry access that new image could be uploaded.

> [!TIPS]
> **Tip**   
> A guide to setup private registry could be found [here](https://distribution.github.io/distribution/)

## k8s cluster storage
A **cluster wide** [k8s StorageClass provisioner](https://kubernetes.io/docs/concepts/storage/storage-classes/) that supports filesystem volume like [NFS](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner) is required. 

## kubevirt
KNL uses [kubevirt](https://kubevirt.io/) to support all VM node types like vSIM, VSR ..etc. so following kubevirt related software must be installed in the cluster:
- [install kubevirt](https://kubevirt.io/user-guide/cluster_admin/installation/): 1.6.3+
- [install kubevirt Containerized Data Importer](https://github.com/kubevirt/containerized-data-importer): 1.63.1+

> [!TIPS]
> **Tip**  
> In case of a insecure registry is used for KNL, add it into `cdi` CR as described [here](https://github.com/kubevirt/containerized-data-importer/blob/main/doc/image-from-registry.md#insecure-registry)

Following [kubevirt feature gates](https://kubevirt.io/user-guide/cluster_admin/activating_feature_gates/) must be enabled:

- Sidecar
- NetworkBindingPlugins
- HostDevices
- Root
- CpuManager

Register kubevirt macvtap binding plugin as described [here](https://kubevirt.io/user-guide/network/net_binding_plugins/macvtap/#macvtap-registration)

## multus
Install multus as described [here](https://github.com/k8snetworkplumbingwg/multus-cni)

## k8slan
k8slan provide unerlying connectity between lab nodes, read the [Prerequisites](https://github.com/hujun-open/k8slan#prerequisites) before [install](https://github.com/hujun-open/k8slan#installation-1).

verion v0.0.7+

## CNI plugin

Following standard CNI plugin are required:
- [bridge](https://www.cni.dev/plugins/current/main/bridge/)

> [!TIPS]
> **Tip**  
> install containernetworking cni plugins including bridge could be done via [CNI installer](https://github.com/hujun-open/installcni)

## Cert manager

[install cert-manager](https://cert-manager.io/docs/installation/)

## cdtool (optional)
[cdtool](https://github.com/hujun-open/cdtool) is a tool that converts VM disk image into container disk image that KNL could use.