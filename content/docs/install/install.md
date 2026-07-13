---
title: "Install KNL"
type: "docs"
bookToc: false
bookFlatSection: true
weight: 2
---
# Install KNL

Two installation paths are available:

- **Scripts:** bootstrap a single-node k3s cluster and install KNL prerequisites and the operator in one workflow.
- **Manual:** install each component step by step on an existing cluster.

## Install with scripts (k3s)

{{% steps %}}

1. ## make sure all [requirements]({{< relref "docs/install/requirements/" >}}) are met
    Linux host with sudo, `curl`, and outbound network access.

1. ## clone the [knl repository](https://github.com/hujun-open/knl)
    ```bash
    git clone https://github.com/hujun-open/knl.git
    cd knl
    ```

1. ## run the install scripts
    One command installs k3s and all KNL components:
    ```bash
    sudo ./scripts/setup-k3s.sh --install
    ```
    Or run in two steps (cluster first, then KNL):
    ```bash
    sudo ./scripts/setup-k3s.sh
    source ./scripts/knl-env.sh
    ./scripts/install-knl.sh
    ```

    **Components installed:**

    Cluster and host (via `setup-k3s.sh`):
    - k3s (single-node Kubernetes)
    - Local image registry (`registry:2`, NodePort `30500` by default; omitted with `--skip-registry`)
    - Helm and k9s (host binaries)
    - IPv6 forwarding enabled on the host (required by k8slan)

    KNL stack (via `install-knl.sh`):
    - cert-manager `v1.17.2`
    - KubeVirt `v1.6.3` and CDI `v1.63.1` (with KNL-required feature gates and macvtap tap binding)
    - Multus CNI
    - containernetworking CNI plugins (e.g. bridge)
    - k8slan
    - KNL operator in namespace `knl-system`, including `knl-pvc` PVC (`10Gi`, storage class `local-path` by default)
    - `knlcli` installed to `/usr/local/bin/knlcli`

    Default versions can be overridden via `KNL_*` env vars. You still need to meet the [requirements]({{< relref "docs/install/requirements/" >}}) for the underlying host.

1. ## create a KNLConfig
    Create a `KNLConfig` YAML with your desired [configuration]({{< relref "docs/usage/knlconfig/" >}}) and apply it:
    ```bash
    kubectl -n knl-system apply -f knlcfg.yaml
    ```

1. ## update `knl-sftp` secret (optional)
    `knl-sftp` secret in `knl-system` namespace holds the login credential for KNL SFTP server; by default both username and password is `knlftp`.
    ```bash
    kubectl -n knl-system edit secret knl-sftp
    ```

1. ## provision licenses
    Some node types require a license secret. Example for vSIM:
    ```bash
    kubectl -n knl-system create secret generic vsimlic --from-file=license=<lic_file>
    ```
    See [node types]({{< relref "docs/nodes/" >}}) for default secret names.

1. ## create a Lab
    Define your topology in a `Lab` CR YAML and apply it:
    ```bash
    kubectl -n knl-system apply -f lab.yaml
    ```
    See the [quickstart]({{< relref "docs/usage/quickstart/" >}}) for an example.

{{% /steps %}}

## Manual install

Use this path when you already have a cluster or need full control over each component.

{{% steps %}}

1. ## make sure all [requirements]({{< relref "docs/install/requirements/" >}}) are met

1. ## create a k8s namespace "knl-system"
    `knl-system` is the namespace where KNL operator and all KNL lab related resources are.
    ```bash
    kubectl create ns knl-system
    ```
1. ## create a PVC with name `knl-pvc` in namespace `knl-system`
    The PVC holds following files:
    - Nokia SROS images for vSIM, VSR and MAG-c with remote file server hosting
    - Nokia SROS config files
    - cache for vSIM, VSR and MAG-c licenses
    
    Following is an example PVC, adjust the `storage` and `storageClassName` accordingly to your setup:
    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
    name: knl-pvc
    namespace: knl-system
    spec:
    accessModes:
        - ReadWriteOncePod
    resources:
        requests:
        storage: 10Gi 
    storageClassName: standard 
    ```
    Create the above PVC via `kubectl`:
    ```bash
    kubectl apply -f knlpvc.yaml
    ```

1. ## install knl via manifest
    install latest version
    ```bash
    kubectl apply -f https://github.com/hujun-open/knl/releases/latest/download/all.yaml
    ```
1. ## update `knl-sftp` secret (optional)
    `knl-sftp` secret in `knl-system` namespace holds the login crendetial for KNL SFTP server, by default both username and password is `knlftp`; 
1. ## create a KNLConfig 
    KNLConfig is the CR for the KNL configuration, create the YAML file with desired
    [configuration]({{< relref "docs/usage/knlconfig/" >}}) and apply it using `kubectl`
    ```bash
    kubectl -n knl-system apply -f knlcfg.yaml 
1. ## download `knlcli`
    `knlcli` is the command line tool to manage KNL lab. download the latest version and install it to `/usr/local/bin/`:
    ```bash
     curl -L https://github.com/hujun-open/knlcli/releases/latest/download/knlcli-linux-x86-64 -o knlcli
     sudo install knlcli /usr/local/bin/

    ```

1. ## provison licenses 
    some node types requires license to run, provision license as corresponding k8s secret with "license" as the key, following is an example of provision vSIM license using default secret name:

    ```bash
    kubectl -n knl-system create secret generic vsimlic --from-file=license=<lic_file>
    ```
    check the [corresponding node type doc]({{< relref "docs/nodes/" >}}) for defautl secret name.
    
{{% /steps %}}
