# API Reference

## Packages
- [knl.kubenetlab.net/v1beta1](#knlkubenetlabnetv1beta1)


## knl.kubenetlab.net/v1beta1

Package v1beta1 contains API Schema definitions for the knl v1beta1 API group.

### Resource Types
- [KNLConfig](#knlconfig)
- [KNLConfigList](#knlconfiglist)
- [Lab](#lab)
- [LabList](#lablist)



#### Connector



Connector specifies a node name and how it connects to the link



_Appears in:_
- [Link](#link)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `node` _string_ | name of node connects to the link |  |  |
| `port` _string_ | used by srsim for mda port id, by SRVM for IOM slot id and by SRL for interface id |  |  |
| `addrs` _string array_ | a list of IP prefix in format `xxxx/yy`, use by node type pod and vm |  |  |
| `routes` _string array_ | a list of static routes in format `<prefix> via <nexthop>`, use by node type pod and vm |  |  |
| `mac` _string_ | interface MAC address of the connecting node, used by node type vm |  |  |


#### GeneralPod



GeneralPod specifies a general k8s pod



_Appears in:_
- [OneOfSystem](#oneofsystem)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `image` _string_ | pod image |  |  |
| `cmd` _string_ | pod's command |  |  |
| `privileged` _boolean_ | privileged pod if true |  |  |
| `pvcSize` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | size of pvc mounted on /root |  |  |
| `memory` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested memory in k8s resource unit |  |  |
| `cpu` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested cpu in k8s resource unit |  |  |


#### GeneralVM



GeneralVM specifies a general kubevirt VM



_Appears in:_
- [OneOfSystem](#oneofsystem)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `memory` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested memory for the VM in k8s resource unit |  |  |
| `cpu` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested cpu for the VM in k8s resource unit |  |  |
| `diskSize` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | the VM disk size in k8s resource unit |  |  |
| `cpuPin` _boolean_ | pin the CPU if true |  |  |
| `hugePage` _boolean_ | request hugepage memory if true |  |  |
| `image` _string_ | kubevirt CDI supported URL, either HTTP (http://) or registry source (docker://) |  |  |
| `init` _string_ | intilization method, supports cloud-init or ignition |  |  |
| `ports` _[Port](#port)_ | listening port of the VM on the 1st pod interface |  |  |
| `user` _string_ | username to login into VM, username and password are feed into vm initialization mechinism like cloud-init |  |  |
| `passwd` _string_ | password to login into VM |  |  |


#### KNLConfig



KNLConfig is the Schema for the configuration of KNL operator



_Appears in:_
- [KNLConfigList](#knlconfiglist)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `knl.kubenetlab.net/v1beta1` | | |
| `kind` _string_ | `KNLConfig` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `spec` _[KNLConfigSpec](#knlconfigspec)_ | spec defines the desired state of KNLConfig |  |  |


#### KNLConfigList



KNLConfigList contains a list of KNLConfig





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `knl.kubenetlab.net/v1beta1` | | |
| `kind` _string_ | `KNLConfigList` | | |
| `metadata` _[ListMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#listmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `items` _[KNLConfig](#knlconfig) array_ |  |  |  |


#### KNLConfigSpec



KNLConfigSpec specifies KNL operator's configuration



_Appears in:_
- [KNLConfig](#knlconfig)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `fileSvr` _string_ | SFTPSever address, must have format as addr/hostname:port |  |  |
| `vxlanGrp` _string_ | multicast address used by VxLAN tunnel between k8s workers |  |  |
| `defaultVxlanDev` _string_ | default VxLAN device name, used if not specified in vxlanDevMap |  |  |
| `vxlanDevMap` _object (keys:string, values:string)_ | a map between k8s worker name and its interface name used as VxLAN device |  |  |
| `storageClass` _string_ | name of k8s storageclass used to create PVCs |  |  |
| `srCPMLoaderImage` _string_ | CPM loader container image, used by vsim, vsri and magc |  |  |
| `srIOMLoaderImage` _string_ | IOM loader container image, used by vsim and magc |  |  |
| `sideCarImage` _string_ | Kubevirt sidecar hook image, used by vsim, vsri and magc |  |  |
| `defaultNode` _[OneOfSystem](#oneofsystem)_ | defaultNode specifies default values for types of node |  |  |




#### Lab



Lab is the Schema for the labs API



_Appears in:_
- [LabList](#lablist)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `knl.kubenetlab.net/v1beta1` | | |
| `kind` _string_ | `Lab` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `spec` _[LabSpec](#labspec)_ | spec defines the desired state of Lab |  |  |


#### LabList



LabList contains a list of Lab





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `knl.kubenetlab.net/v1beta1` | | |
| `kind` _string_ | `LabList` | | |
| `metadata` _[ListMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#listmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `items` _[Lab](#lab) array_ |  |  |  |


#### LabSpec



LabSpec defines the desired state of Lab



_Appears in:_
- [Lab](#lab)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `nodes` _object (keys:string, values:[OneOfSystem](#oneofsystem))_ | nodes lists all nodes in the lab |  |  |
| `links` _object (keys:string, values:[Link](#link))_ |  |  |  |




#### Link



Link defines a layer2 connection between nodes,
two or more nodes per link are supported.



_Appears in:_
- [LabSpec](#labspec)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `nodes` _[Connector](#connector) array_ | a list of nodes connect to the link's layer2 network |  |  |


#### MAGC

_Underlying type:_ _[SRVM](#srvm)_

MAGC specifies a Nokia MAG-c



_Appears in:_
- [OneOfSystem](#oneofsystem)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `chassis` _[SRChassis](#srchassis)_ | specifies chassis configuration |  |  |
| `image` _string_ | one of three types of image loading method:<br />1.docker image url like "exampleregistry/sros:25.10.1";<br />2.sub folder name of the SROS/MAGC image when start with "filesvr:", like "filesvr:25.10.1" |  |  |
| `diskSize` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | Disk size for the CPM, only used when image is a docker image, must >= image size |  |  |
| `license` _string_ | a k8s secret name contains license with key "license" |  |  |
| `uuid` _string_ | VM's firmware UUID |  |  |
| `dedicate` _boolean_ | if true, allocate dedicate cpu and huge page memory;<br />recommand to set to true in case of vsr and magc |  |  |


#### NodeType

_Underlying type:_ _string_





_Appears in:_
- [SRChassis](#srchassis)

| Field | Description |
| --- | --- |
| `pod` |  |
| `srl` |  |
| `srsim` |  |
| `vsim` |  |
| `vsri` |  |
| `magc` |  |
| `unknown` |  |
| `vm` |  |


#### OneOfSystem



OneOfSystem specifies one KNL node type, only one field should be specified.



_Appears in:_
- [KNLConfigSpec](#knlconfigspec)
- [LabSpec](#labspec)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `vsim` _[VSIM](#vsim)_ |  |  |  |
| `vsri` _[VSRI](#vsri)_ |  |  |  |
| `magc` _[MAGC](#magc)_ |  |  |  |
| `vm` _[GeneralVM](#generalvm)_ |  |  |  |
| `srl` _[SRLinux](#srlinux)_ |  |  |  |
| `pod` _[GeneralPod](#generalpod)_ |  |  |  |
| `srsim` _[SRSim](#srsim)_ |  |  |  |


#### SRCard



SRCard is a CPM or IOM card



_Appears in:_
- [SRChassis](#srchassis)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `type` _string_ | Card model |  |  |
| `sysinfo` _string_ | sysinfo is only used by vsim, mag-c and vsri, not need to specify in most cases; |  |  |
| `mdas` _string_ | list of MDAs that are insert directly into card without XIOM; mdas and xioms are mutully exclusive |  |  |
| `xioms` _object (keys:string, values:[XIOM](#xiom))_ | list of XIOMs; key is XIOM slot id, e.g. x1/x2; mdas and xioms are mutully exclusive |  |  |
| `memory` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested memory in k8s resouce unit |  |  |
| `cpu` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested CPU in k8s resource unit |  |  |
| `ports` _[Port](#port)_ | list of listening ports for management interface |  |  |


#### SRChassis



SRChassis is used by srsim, vsim, vsri and magc to specify the chassis configuration.



_Appears in:_
- [SRSim](#srsim)
- [SRVM](#srvm)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `model` _string_ | chassis model |  |  |
| `cards` _object (keys:string, values:[SRCard](#srcard))_ | a dictionary of CPM and IOM cards,<br />key is slot id, "A","B" for CPM, number for IOM |  |  |
| `sfm` _string_ | SFM model |  |  |
| `chassisMac` _string_ | Chassis Base MAC address, auto assigned if not specified |  |  |




#### SRLinux



SRLinux specifies a Nokia SRLinux chassis;



_Appears in:_
- [OneOfSystem](#oneofsystem)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `image` _string_ | SRLinux container image |  |  |
| `chassis` _string_ | chassis model |  |  |
| `license` _string_ | a k8s secret contains the license file with "license" as the key |  |  |
| `memory` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested memory in k8s resource unit |  |  |
| `cpu` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | requested cpu in k8s resource unit |  |  |


#### SRSim



SRSIM creates a Nokia SR-SIM;
note: it is important to set `tx-checksum-ip-generic` off in corresponding bridge interface, otherwise IP traffic toward management interface won't work
in kind, it is docker bridge;
in general k8s, it is cni0 bridge in each worker;
"ethtool -K <interface> tx-checksum-ip-generic off"
see SR-SIM installation guide for details



_Appears in:_
- [OneOfSystem](#oneofsystem)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `image` _string_ | Docker image |  |  |
| `chassis` _[SRChassis](#srchassis)_ | specifies the chassis configuration |  |  |
| `license` _string_ | name of k8s secret contains license file with "license" as the key |  |  |


#### SRVM



undelying type for VSIM, VSRI, and MAGC



_Appears in:_
- [MAGC](#magc)
- [VSIM](#vsim)
- [VSRI](#vsri)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `chassis` _[SRChassis](#srchassis)_ | specifies chassis configuration |  |  |
| `image` _string_ | one of three types of image loading method:<br />1.docker image url like "exampleregistry/sros:25.10.1";<br />2.sub folder name of the SROS/MAGC image when start with "filesvr:", like "filesvr:25.10.1" |  |  |
| `diskSize` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | Disk size for the CPM, only used when image is a docker image, must >= image size |  |  |
| `license` _string_ | a k8s secret name contains license with key "license" |  |  |
| `uuid` _string_ | VM's firmware UUID |  |  |
| `dedicate` _boolean_ | if true, allocate dedicate cpu and huge page memory;<br />recommand to set to true in case of vsr and magc |  |  |


#### VSIM

_Underlying type:_ _[SRVM](#srvm)_

VSIM specifies a Nokia vSIM router



_Appears in:_
- [OneOfSystem](#oneofsystem)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `chassis` _[SRChassis](#srchassis)_ | specifies chassis configuration |  |  |
| `image` _string_ | one of three types of image loading method:<br />1.docker image url like "exampleregistry/sros:25.10.1";<br />2.sub folder name of the SROS/MAGC image when start with "filesvr:", like "filesvr:25.10.1" |  |  |
| `diskSize` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | Disk size for the CPM, only used when image is a docker image, must >= image size |  |  |
| `license` _string_ | a k8s secret name contains license with key "license" |  |  |
| `uuid` _string_ | VM's firmware UUID |  |  |
| `dedicate` _boolean_ | if true, allocate dedicate cpu and huge page memory;<br />recommand to set to true in case of vsr and magc |  |  |


#### VSRI

_Underlying type:_ _[SRVM](#srvm)_

VSRI specifies a Nokia VSR-I router



_Appears in:_
- [OneOfSystem](#oneofsystem)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `chassis` _[SRChassis](#srchassis)_ | specifies chassis configuration |  |  |
| `image` _string_ | one of three types of image loading method:<br />1.docker image url like "exampleregistry/sros:25.10.1";<br />2.sub folder name of the SROS/MAGC image when start with "filesvr:", like "filesvr:25.10.1" |  |  |
| `diskSize` _[Quantity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#quantity-resource-api)_ | Disk size for the CPM, only used when image is a docker image, must >= image size |  |  |
| `license` _string_ | a k8s secret name contains license with key "license" |  |  |
| `uuid` _string_ | VM's firmware UUID |  |  |
| `dedicate` _boolean_ | if true, allocate dedicate cpu and huge page memory;<br />recommand to set to true in case of vsr and magc |  |  |


#### XIOM



SR XIOM



_Appears in:_
- [SRCard](#srcard)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `type` _string_ | XIOM model |  |  |
| `mdas` _string array_ | list of MDAs insert into the XIOM |  |  |


