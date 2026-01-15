---
title: "VSR"
type: "docs"
bookToc: true
bookFlatSection: true
weight: 3
---
# Overview
Node type `vsri` represents Nokia VSR VNF, running SR OS. the configuration is mostly same as [`vsim`]({{< relref "docs/nodes/vsim" >}}) with following differences:

1. the chassis model is `VSR-I`, refer to the VSR VNF product documentation for supported card/mda model.
2. For optimal performance, set `dedicate` to true, which enables VM to use CPU pinning and huge page memory. It requires k8s worker node to support.
3. VSR requires license to run, which is different from vsim. default secret name is `vsrlic`.