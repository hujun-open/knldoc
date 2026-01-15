---
title: "MAG-c"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 4
---
# Overview
Node type `magc` represents Nokia MAG-c. the configuration is mostly same as [`vsim`]({{< relref "docs/nodes/vsim" >}}) with following differences:

1. the chassis model is `VSR`, refer to the MAG-c product documentation for supported card/mda model.
    > [!NOTE]
    > **Note**  
    > The MAG-c card terminology is different from vSIM: OAM-VM is equivalent of CPM; LB-VM and SM-VM is equivalent of IOM

2. For optimal performance, set `dedicate` to true, which enables VM to use CPU pinning and huge page memory. It requires k8s worker node to support.
3. MAG-c requires license to run, which is different from vsim. default secret name is `magclic`

## Limitations
In case of using MAG-c images on a SFTP server, the default ftp config file location doesn't work, a local CF card based location should be specificed by using MAG-c command `bof primary-config`.