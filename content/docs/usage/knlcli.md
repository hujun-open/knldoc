---
title: "knlcli"
type: "docs"
bookToc: false
bookFlatSection: true
weight: 20
---
# Overview
`knlcli` is the command line tool to manage  KNL labs. 

```
KNL CLI tool

Usage:
  knlcli [command]

Available Commands:
  completion  Generate the autocompletion script for the specified shell
  config      save/load configuration
  console     connect to the console of specified node in the specified lab
  create      create a lab via the specified YAML file
  exec        run a command on the specified node and print output
  export-disk export General VM disk to qcow2 file
  help        Help about any command
  rm          remove a lab
  shell       connect to the specified node in the specified lab
  show        show existing Lab info
  topo        generate lab topology in D2 format

Flags:
  -h, --help              help for knlcli
      --kubeconf string   path to k8s config (default "/home/hj/.kube/config")
  -n, --ns string         k8s namespace (default "knl-system")
  -v, --version           version for knlcli

Use "knlcli [command] --help" for more information about a command.

```


## Installation

[see here]({{< relref "docs/install/install/#download-knlcli" >}})

## Shell completion
`knlcli` supports bash completion, enable it via following:
```bash
sudo apt-get install -y bash-completion # install bash-completion if not already installed
knlcli completion bash | sudo tee /etc/bash_completion.d/knlcli # install the knlcli completion script
source <(knlcli completion bash) # activate the completion for current shell session
```

## Create a lab
`knlcli create <labdef.yaml>`, this is equivalent to `kubectl apply -f <labdef.yaml>`.

## List existing labs
`knlcli show` by default list name of existing labs:
```bash
user@svr-1:~$ knlcli show
example-lab
srl-basic
srsim  
```
`knlcli show <labname> -v` show lab details:


```bash
user@svr-1:~$ knlcli show example-lab -v
example-lab:
   Node      Type    Chassis   Pods                                       Worker/PodIP
   srsim-1   SRSIM   SR-7      example-lab-srsim-1                        worker-1/10.244.2.92
   vsim-1    VSIM    SR-7      virt-launcher-example-lab-vsim-1-1-cjfn6   worker-2/10.244.0.95
                               virt-launcher-example-lab-vsim-1-a-lvhqd   worker-2/10.244.0.115
   
   Link      Nodes    Port
   link1     srsim-1
             vsim-1
```
## Access a lab node via shell command

`knlcli shell <lab> <node>`

- vSIM, VSR, MAG-c, SR-SIM, SRLinux: this command creates a SSH session to the control card (e.g. CPM).
- General VM: this command creates a SSH sesion to the VM
- General pod: this command command trys to execute bash inside the pod

## Run a command on a lab node

`knlcli exec <lab> <node> "<cmd>" [--username <user>] [--passwd <pass>]`

Run a single command on the specified node, print its output, and exit. Unlike `shell`, this does not open an interactive session. Quote the command when it contains spaces.

| Node type | Transport | CLI / shell | Default credentials |
|-----------|-----------|-------------|---------------------|
| vSIM, VSRI | SSH to CPM | MD-CLI | `admin` / `admin` |
| MAG-c | SSH to CPM | Classic CLI | `admin` / `admin` |
| SR-SIM | SSH to pod | MD-CLI | `admin` / `admin` |
| SRLinux | SSH | SRLinux CLI | `admin` / `NokiaSrl1!` |
| General VM | SSH | OS shell | from VM spec (`user` / `password`) |
| General pod | `kubectl exec` | `sh -c` | none |

- `--username` and `--passwd` override the defaults above (same flags as `shell`).
- General pod requires `kubectl` on the client machine (same dependency as `console` for pod types).

```bash
user@svr-1:~$ knlcli exec ipsec-basic srsim-1 "show version"
TiMOS-C-25.10.R2 ...

user@svr-1:~$ knlcli exec quickstart srl-1 "show version"
...
```

## Access a lab node via console command

`knlcli console <lab> <node>`

- vSIM, VSR, MAG-c: this command creates a console session to the control card (e.g. CPM).
- General VM: this command creates a console sesion to the VM
- SR-SIM, SRLinux, general pod: this command command trys to execute bash inside the pod

## Remove lab
- `knlcli remove <lab>`: remove the specified lab
- `knlcli remove --all`: remove all labs

  > [!NOTE]
  > **Note**  
  > Removing a lab won't remove the perisisten storage of nodes in the lab.

## Lab save/load
see [Lab save/load]({{< relref "docs/usage/cfgmgmt/" >}})

## Export General VM disk

`knlcli export-disk <lab.yaml> <node> --worker <k8s-node> --host-dir <abs-dir> [-o <file.qcow2>] [--image <helper>]`

Export the persistent disk of a General VM node to a qcow2 file on a **worker node's local filesystem**. The lab YAML resolves the lab name, namespace, and node type; the disk is read from the CDI PVC in the cluster (`{lab}-{node}`). A helper pod runs on `--worker`, converts with `qemu-img`, and writes the result into `--host-dir` via a `hostPath` mount (no stream through the API server).

**Prerequisite:** the VMI must not be running. Typically remove the lab first (`knlcli rm <lab>`); the DataVolume/PVC remains after lab deletion. Choose a worker with enough **local** disk space for the output qcow2.

```bash
user@svr-1:~$ knlcli rm vm-example
vm-example has been removed
user@svr-1:~$ knlcli export-disk vm-example.yaml vm-debian \
  --worker worker-1 --host-dir /data/knl-export -o vm-debian.qcow2
2026/07/23 10:30:00 creating export helper pod knlcli-export-xxxxxxxx on worker worker-1
2026/07/23 10:30:05 waiting for export helper pod knlcli-export-xxxxxxxx to run
2026/07/23 10:30:10 converting disk to worker-1:/data/knl-export/vm-debian.qcow2 (progress from qemu-img -p)
    (100.00/100%)
2026/07/23 10:45:00 exported vm-debian disk to worker-1:/data/knl-export/vm-debian.qcow2 (10737418240 bytes)
```

Collect the file from the worker (for example with `scp` or by reading the path on that host):

```bash
user@svr-1:~$ scp worker-1:/data/knl-export/vm-debian.qcow2 .
```

- `--worker`: Kubernetes node name where the helper pod runs and where the file is written (`spec.nodeName`)
- `--host-dir`: absolute directory on that worker; created if missing (`hostPath` `DirectoryOrCreate`); mounted at `/out` in the helper pod
- `-o` / `--output`: output **filename** under `--host-dir` (default: `<node>.qcow2`)
- `--image`: helper container image with `qemu-img` (default: `ghcr.io/hujun-open/knlcli-export:latest`)


## Show lab topology
`knlcli topo <lab>` displays lab topology in following formats:
1. visualization render in ASCII format (this requires [installation of D2 command line](https://d2lang.com/tour/install/)): `knlcli topo <lab>`
2. [D2](https://d2lang.com/) file: `knlcli topo <lab> --render=false`
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