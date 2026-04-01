---
title: "Lab save/load"
type: "docs"
bookToc: false
bookFlatSection: true
weight: 30
---
# Overview
`knlcli` support save/load lab with CNF/VNF configurations, e.g. SROS configuration; currently this feature supports following node types:
- Nokia vSIM
- Nokia SR-SIM
- Nokia VSR
- Nokia SRLinux
- Nokia MAG-c

> [!NOTE]
> **Note**  
> For vSIM/VSR/SR-SIM, SSH must be enabled and MD-CLI must be the default CLI

## Protocol
The protocol of get/set configuration depends on the node type:

- SRLinux: gNMI
- MAG-c: classic CLI
- vSIM/VSR/SR-SIM: gNMI (`knlcli` will enable gNMI on the node via MD-CLI if it is not enabled)

## Save
Use command `knlcli config save <Lab> [flags]` to save configuration all supporting nodes in the specified lab into a folder, by default, the folder is the `./<lab-name>`, each node's configuration is saved as `<nodeName>.cfg`, the lab manifest is saved as `lab.yaml`. 

Following is an example save lab `ipsec-basic` into folder `./tmp/ipsec-basic`:
```bash
user@svr-1:~$ knlcli config save --output /tmp ipsec-basic
15:19:35 cfg.go:67: saved lab ipsec-basic YAML to /tmp/ipsec-basic/lab.yaml
15:19:35 cfg.go:71: saving config for node srsim-2 ...
15:19:35 cfg.go:95: srsim-2 config is saved as /tmp/ipsec-basic/srsim-2.cfg
15:19:35 cfg.go:71: saving config for node pod-1 ...
15:19:35 cfg.go:86: pod-1 returns empty string, skip saving
15:19:35 cfg.go:71: saving config for node srsim-1 ...
15:19:35 cfg.go:95: srsim-1 config is saved as /tmp/ipsec-basic/srsim-1.cfg
```

### Saved configuration file
The format of saved configuration file depends on the node type:

- SRLinux: json
- VSIM: MD config in json 
- VSR: MD config in json 
- SR-SIM: MD config in json 
- MAG-c: classic CLI


## Load
Use command `knlcli config load <Lab> [flags]` to load configurations from a folder for all supporting nodes in the specified lab. by default, the folder is the `./<lab-name>`, each node's configuration file name is `<nodeName>.cfg`. 

```
knlcli config load -h
load CNF/VNF's configurations

Usage:
  knlcli config load <Lab> [flags]

Arguments:
  <Lab> string    lab name (default "")

Flags:
  -h, --help                 help for load
      --input string         config folder name (default ".")
      --recreate             recreate the lab before load configurations
      --wait time.Duration   wait timeout (default 5m0s)

Global Flags:
      --kubeconf string   path to k8s config (default "/home/hj/.kube/config")
  -n, --ns string         k8s namespace (default "knl-system")
      --passwd string     password
      --user string       username (default "admin")

```

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
> [!NOTE]
> **Note**  
> By default, the lab itself is not created/recreated via `config load`, but the lab could be created/recreated via `--recreate` parameter.