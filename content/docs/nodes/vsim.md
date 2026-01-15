---
title: "vSIM"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 1
---
# Overview
Node type `vsim` represents Nokia vSIM, running SR OS. 


## Configuration
see [KNL API - VSIM]({{< relref "docs/api/#vsim" >}})

## Image
There are two deployment options:
1. SROS image as container image 
2. SROS images on a SFTP server

### SROS image as container image 
If a `image` of a vsim node spec is a container image URL, then the image must be in [Kubevirt container disk format](https://Kubevirt.io/user-guide/storage/disks_and_volumes/#containerdisk-workflow-example) that contains the vSIM qcow2 file from the Nokia vSIM software package, the image is imported as a [Kubevirt Data Volume (DV)](https://Kubevirt.io/user-guide/storage/disks_and_volumes/#datavolume) for persistency storage. and the DV is mounted inside vsim's CPM VM as `cf3`device. 
> [!TIP]
> **Tip**  
> [cdtool](https://github.com/hujun-open/cdtool) is a tool that converts VM disk image into container disk image that KNL could use.

Same container disk image is used for vSIM's IOM VM as well.

### SROS images on a SFTP server
If `image` of a vsim node spec has prefix "filesvr:", then it is a sub folder name on a SFTP server with path "/knlroot/imgs/<sub-folder-name>", the SFTP server address/FQDN and port is specified via `fileSvr` of KNLConfig, by default the SFTP server is the KNL operator built-in one `knl-sftp-service.knl-system.svc.cluster.local:22`; for example if `image: filesvr:25.10.1`, and the `fileSvr` is the default one, then the vsim loads SROS image (`i386*.tim` files) from `sftp://knl-sftp-service.knl-system.svc.cluster.local:22/knlroot/imgs/25.10.1/`.

The login username/password of the sftp server is stored in `knl-sftp` secret, the default value is `knlftp`.

> [!NOTE]
> **NOTE**  
> This option requires cpm/iom load container disk images that are not part of KNL.

## Chassis configuration
The phyiscal router simulated by vSIM could be specified via `chassis` section in the vsim node spec, it contains two parts:

1. spec of chassis itself:
    - `model`: chassis model, like `SR-7`
    - `sfm`: SFM card model, like `m-sfm5-7`
2. cards: it is map of cards, including CPM,IOM;
    - the key is slot id: A, 1, 2, 3 ...etc 
    - value is spec of the card:
        - `type`: card model, like `iom4-e`
        - `mdas`: a list of MDA model in the card
        - `sysinfo`: VM's sysinfo string, no need to specify in most cases;
        - `xioms`: a map of xiom spec, key is the XIOM slot id like `x1`, value is the spec:
            - `type`: xiom model
            - `mdas`: a list of MDAs in the XIOM
        - > [!NOTE]
          > **Note**  
          > `mdas` and `xioms` are mutually exclusive 

For supported chassis/card/mda/xiom, refer to `Appendix A` of `Nokia vSIM Installation and Setup Guide` of corresponding SROS release; here is a [link to SR OS 25.10.R1](https://documentation.nokia.com/sr/25-10/7x50-shared/vsim-installation-setup/appendixes.html#ai89jtwgeh)

Each specified card is a VM, and its Memory & CPU resource could requested per card, by default it is 4GB memory and 2 CPU.

## MDA port mapping

For a given vSIM node, by default the 1st I/O MDA port of 1st IOM (e.g. `1/1/1`) is mapped to the 1st lab link it belongs to, 2nd interface (`1/1/2`) is mapped to the 2nd lab link it belongs to ..so on. 

In example below, `link-1` is before `link-3` in lexicographical order, so first port of first I/O MDA (MDA slot 2) of first IOM slot (slot 2) `2/2/1` of `vsim-4` connects to `pod-1`, `2/2/2` connects to `pod-2`
```YAML
apiVersion: knl.kubenetlab.net/v1beta1
kind: Lab
metadata:
  name: vsim-ftp
  namespace: knl-system
spec:
  links:
    link-3:
      nodes:
      - node: vsim-4
      - node: pod-2
    link-1:
      nodes:
      - node: vsim-4
      - node: pod-1   
  nodes:
    vsim-4:
      vsim:
        uuid: "471a13a0-9ffa-4e0b-899b-ca89712ec021"
        chassis:
          cards:
            "2": # IOM slot #2
              mdas:
              - me-isa2-ms
              - me10-10gb-sfp+  # MDA slot #2            
              type: iom4-e
            A:
              type: cpm5
          model: SR-7
          sfm: m-sfm5-7
        image: filesvr:25.10R1
        license: vsimlic
```

In case of multi-slot chassis, the `port` field in the `Connector` of a lab link could used to specify the **IOM** slot id the corresponding MDA port is at. in the example below, the `vsim-4` MDA port `2/2/1` connects to `pod-2`, while `1/2/1` connects to `pod-1`
```yaml
apiVersion: knl.kubenetlab.net/v1beta1
kind: Lab
metadata:
  name: vsim-ftp
  namespace: knl-system
spec:
  links:
    link-3:
      nodes:
      - node: vsim-4
        port: "2" # IOM slot #2
      - node: pod-2
    link-1:
      nodes:
      - node: vsim-4
        port: "1" # IOM slot #1
      - node: pod-1   
  nodes:
    vsim-4:
      vsim:
        uuid: "471a13a0-9ffa-4e0b-899b-ca89712ec021"
        chassis:
          cards:
            "2": # IOM slot #2
              mdas:
              - me-isa2-ms
              - me10-10gb-sfp+  # MDA slot #2            
              type: iom4-e
            "1": # IOM slot #1
              mdas:
              - me-isa2-ms
              - me10-10gb-sfp+  # MDA slot #2            
              type: iom4-e              
            A:
              type: cpm5
          model: SR-7
          sfm: m-sfm5-7
        image: filesvr:25.10R1
        license: vsimlic

```


## Persistent storage
`CF3:` on CPM VM is mounted by a kubevirt DV:
- in case of SROS image as container image: the DV size is specified by `diskSize` in the node spec. it must >= the container image size, default is 1.5GB.
- in case of SROS image on a SFTP server: the DV size is 64MB

The SROS configuration file location by default is at KNL's builtin SFTP server `/knlroot/cfgs/<lab-name>/<node-name>/`. which means they are persistent even in case lab is removed.

## License
vSIM requires a license to run, specified via `license` field in the node configuration, the value is name of a k8s secrete in `knl-system` hold the license with key `license`, the default secret name is `vsimlic`.

vSIM license needs to match CPM VM's UUID, which could specified via `uuid` field of the node spec.