---
title: "KNL Configuration"
type: "docs"
bookToc: false
bookFlatSection: true
weight: 20
---

# KNL Configuration 
KNL's configuration is a CR `KNLConfig` with name `knlcfg` in `knl-system` namespace, it must be created before creating KNL operator; check full list of spec [here]({{< relref "docs/api/#knlconfigspec" >}}).



It contains two parts:
1. default node configuration (via `defaultNode` section)
2. global configuration items, which are all other items.


## Mandatory configurations
Most of configuration items have default values, however following global items are mandatory for user input:

1. `storageClass`
2. `defaultVxlanDev` and/or `vxlanDevMap`

## Default node configuration
Default configuration for each node type could be specified in the `defaultNode` section, which would be used to default missing values of node in the `lab` CR. if there is no user input in the `defaultNode` section, then application default values will apply.

So the default hierchical is following:

`applicaton_default_values -> defaultNode_in_knlcfg -> node_spec_in_lab`