---
title: "Quickstart"
type: "docs"
bookToc: false
bookFlatSection: true
weight: 1
---
# Quickstart
This quickstart guide walks through steps of creating a simple lab, introduces some key concepts along the way.

{{% steps %}}
0. ## install KNL
    Referes to the [Install KNL]({{< relref "docs/install/install/" >}})
1. ## creates a `lab.yaml`
    This file defines a lab named `quickstart` contains two SRLinux nodes, using 25.10.1 release image.
    ```yaml
    apiVersion: knl.kubenetlab.net/v1beta1
    kind: Lab
    metadata:
      name: quickstart
      namespace: knl-system
    spec:
      nodes:
        srl-2:
          srl:
            image: ghcr.io/nokia/srlinux:25.10.1
            chassis: ixr-d2l
      links:
        link1:
          nodes:
          - node: srl-1
          - node: srl-2
    ```
    There are two parts of a lab defintion:
    - nodes: define the configuration for each node in the topology, it is a map: key is the node name while value is the type specific configuration. This YAML specifies a node with name `srl-2` and its type is `srl`, along with `image` and `chassis` specification.
      - node name **must** have the prefix: `<nodeType>-`, like `srl-1` means it is a SRLinux node; see list of supported node types in [Node Types]({{< relref "docs/nodes/" >}}) 
      - the actual configruation depends on node type, see corresponding type guide under [Node Types]({{< relref "docs/nodes/" >}}) section.
      - certain per node configuration are optional like `memory` in SRLinux case, the default value are specified in `KNLConfig` CR.

      - > [!NOTE]
        > **Note**  
        > node `srl-1` is not defined in `nodes` section, which means it will use default configuration provided by `KNLConfig` CR.
    - links: defines how nodes are connected, it is a map: key is a link name while value is the specification of the link, `nodes` is a mandatory part of it, which is a list of node names connects to the same L2 network of the link.
      


1. ## create the lab

    Either use KNL cli tool `knlcli`:
    ```bash
    knlcli create lab.yaml
    ```
    Or use `kubectl`:
    ```bash
    kubectl apply -f lab.yaml
    ```
1. ## check the created lab
    ```bash
    user@svr-1:~$ knlcli show
    quickstart:
      Node    Type   Chassis      Pods               Worker/PodIP
      srl-1   SRL    ixr-h5-32d   quickstart-srl-1   kind-worker/10.244.1.21
      srl-2   SRL    ixr-d2l      quickstart-srl-2   kind-worker/10.244.1.22
      Link    Nodes
      link1   srl-1
              srl-2
    ```
    or use `kubectl`
    ```bash
    user@svr-1:~$  kubectl -n knl-system get lab quickstart -o yaml
    apiVersion: v1
    items:
    - apiVersion: knl.kubenetlab.net/v1beta1
      kind: Lab
      metadata:
        annotations:
          kubectl.kubernetes.io/last-applied-configuration: |
            {"apiVersion":"knl.kubenetlab.net/v1beta1","kind":"Lab","metadata":{"annotations":{},"name":"quickstart","namespace":"knl-system"},"spec":{"links":{"link1":{"nodes":[{"node":"srl-1"},{"node":"srl-2"}]}},"nodes":{"srl-2":{"srl":{"chassis":"ixr-d2l","image":"ghcr.io/nokia/srlinux:25.10.1"}}}}}
        creationTimestamp: "2025-12-31T17:57:59Z"
        finalizers:
        - lab.kubenetlab.net/finalizer
        generation: 1
        name: quickstart
        namespace: knl-system
        resourceVersion: "112671"
        uid: cc4d09dc-bc96-48db-a738-af6cfdeaa5c3
      spec:
        links:
          link1:
            nodes:
            - mac: "12:20:22:00:00:01"
              node: srl-1
            - mac: "12:20:22:00:00:02"
              node: srl-2
        nodes:
          srl-1:
            srl:
              chassis: ixr-h5-32d
              image: ghcr.io/nokia/srlinux:25.10
              memory: 4Gi
          srl-2:
            srl:
              chassis: ixr-d2l
              image: ghcr.io/nokia/srlinux:25.10.1
              memory: 4Gi
    kind: List
    metadata:
      resourceVersion: ""

    ```
    > [!NOTE]
    > **Note**  
    > the created lab has more configuration than the YAML file (like `memory`, `mac`), most of the addtional parts are the default values provided by `KNLConfig` CR, others like `mac` are dynamic default values.

1. ## login into node
    Use `knlcli shell` command to ssh into node `srl-1` using default username `admin`, password is the default SRLinux password `NokiaSrl1!`.
    ```bash

    user@svr-1:~$ knlcli shell quickstart srl-1
    connecting to srl-1 at 10.244.1.21 username admin
    Warning: Permanently added '10.244.1.21' (ED25519) to the list of known hosts.
    (admin@10.244.1.21) Password:
    Loading environment configuration file(s): ['/etc/opt/srlinux/srlinux.rc']
    Welcome to the Nokia SR Linux CLI.

    --{ [FACTORY] + running }--[  ]--
    A:admin@quickstart-srl-1#
    ```
    or use `ssh` command with corresponding pod IP directly:
    ```bash
    ssh admin@10.244.1.21
    ```
1. ## remove the lab
    use `knlcli`
    ```bash
    knlcli remove quickstart 
    ```    
    or use `kubectl`
    ```bash
    kubectl -n knl-system delete lab quickstart
    ```
    > [!NOTE]
    > **Note**  
    > removing a lab won't remove the persistent part of the lab, like its data volume or PVC

{{% /steps %}}