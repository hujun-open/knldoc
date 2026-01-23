---
title: "Documentation"
type: "docs"
bookToc: false
bookFlatSection: true
---

{{< figure src="logo.png" alt="KNL Logo" width="160px" >}}

# KubeNetLab

KubeNetLab (KNL) is a k8s operator creates and manages virtual network topology consists of VNF/CNF for learning and testing purposes. 

KNL has following key features:

1. Running on top of k8s with capability of to scale out to entire k8s cluster. e.g. a single lab topology could span across multiple servers.
2. Topology is defined via KNL `Lab` CR, standard k8s tool like `kubectl` or `k9s` could be used to create/manage labs, while KNL cli tool `knlcli` provides more user friendly CLI operations.
3. Supports both container and VM, following are node types supported today:
    - Nokia vSIM
    - Nokia SR-SIM
    - Nokia VSR-I
    - Nokia MAG-c
    - Nokia SRLinux
    - General Virtual Machine
    - General pod

Follow YAML defines a simple example-lab contains a Nokia SR-SIM and a Nokia vSIM, with default chassis configuration, connects to each other via a virtual link `link1`.

```yaml
apiVersion: knl.kubenetlab.net/v1beta1
kind: Lab
metadata:
    name: example-lab
    namespace: knl-system
spec:
  links:
    link1:
      nodes:
      - node: vsim-1
      - node: srl-1
    link2:
      nodes:
      - node: srl-1
      - node: srsim-1
    link3:
      nodes:
      - node: srsim-1
      - node: vsim-1
```


Use KNL command line tool `knlcli` to check the created Lab
```bash
user@svr-1:~$ knlcli show
example-lab:
   Node      Type    Chassis                           Pods                                       Worker/PodIP
   srl-1     SRL     ixr-d3l                           example-lab-srl-1                          worker-2/10.244.0.137
   srsim-1   SRSIM   SR-7                              example-lab-srsim-1                        worker-1/10.244.3.77
   vsim-1    VSIM    SR-7                              virt-launcher-example-lab-vsim-1-1-nprdm   worker-3/10.244.2.91
                                                       virt-launcher-example-lab-vsim-1-a-vqjqq   worker-3/10.244.2.90
   Link      Nodes
   link1     vsim-1
             srl-1
   link2     srl-1
             srsim-1
   link3     srsim-1
             vsim-1
```
  > [!NOTE]
  > **Note**  
  > Three lab nodes(`srsim-1`, `vsim-1` and `srl-1`) run on three different servers (`worker-1` to `worker-3`), and 3 links span across all servers.

  Use `knlcli topo` show the topology in visualized format:

  ```bash
  user@svr-1:~$ knlcli topo example-lab
      ┌───────┐
      │vsim-1 │
      │       │
      └───────┘
          │  │
          │  └─┐
          │    │
   ┌──────┐    │
   │srl-1 │    │
   │      │    │
   └──────┘    │
          │    │
          │  ┌─┘
          │  │
      ┌────────┐
      │srsim-1 │
      │        │
      └────────┘

  ```