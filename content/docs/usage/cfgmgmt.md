---
title: "CNF/VNF configuration management"
type: "docs"
bookToc: false
bookFlatSection: true
weight: 30
---
# Overview
`knlcli` support save/load VNF/CNF configurations, e.g. SROS configuration, currently this feature supports following node types:
- Nokia vSIM
- Nokia SR-SIM
- Nokia VSR-I

> [!NOTE]
> **Note**  
> For SROS system, MD-CLI and SSH must be enabled 

## Save
Use command `knlcli config save <Lab> [flags]` to save configuration all supporting nodes in the specified lab into a folder, by default, the folder is the `./<lab-name>`, each node's configuration is saved as `<nodeName>.cfg`. 

Following is an example save lab `ipsec-basic` into folder `./tmp/ipsec-basic`:
```bash
user@svr-1:~$ knlcli config save --output ./tmp ipsec-basic
20:16:27 cfg.go:27: saving config for node pod-1 ...
20:16:27 cfg.go:37: pod-1 returns empty string, skip saving
20:16:27 cfg.go:27: saving config for node srsim-1 ...
20:16:27 cfg.go:50: srsim-1 config is saved as tmp/ipsec-basic/srsim-1.cfg
20:16:27 cfg.go:27: saving config for node srsim-2 ...
20:16:27 cfg.go:50: srsim-2 config is saved as tmp/ipsec-basic/srsim-2.cfg
```

## Load
Use command `knlcli config load <Lab> [flags]` to load configurations from a folder for all supporting nodes in the specified lab. by default, the folder is the `./<lab-name>`, each node's configuration file name is `<nodeName>.cfg`. 

Following is an example loading config files from folder `./tmp/ipsec-basic`  for lab `ipsec-basic`:

```bash
user@svr-1:~$  knlcli config load --input ./tmp ipsec-basic
20:19:39 cfg.go:67: loading config from folder tmp/ipsec-basic...
20:19:39 cfg.go:70: reading from tmp/ipsec-basic/pod-1.cfg
20:19:39 cfg.go:74: pod-1's config file not found, skip
20:19:39 cfg.go:70: reading from tmp/ipsec-basic/srsim-1.cfg
20:19:40 cfg.go:92: loaded config for srsim-1
20:19:40 cfg.go:70: reading from tmp/ipsec-basic/srsim-2.cfg
20:19:42 cfg.go:92: loaded config for srsim-2
```