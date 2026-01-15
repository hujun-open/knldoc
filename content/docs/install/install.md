---
title: "Install KNL"
type: "docs"
bookToc: false
bookFlatSection: true
weight: 2
---
# Install KNL

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
    kubectl apply -f kubectl apply -f https://github.com/hujun-open/k8slan/releases/latest/download/all.yaml
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
     curl -L https://github.com/hujun-open/knl/releases/latest/download/knlcli-linux-x86-64 -o knlcli
     sudo install knlcli /usr/local/bin/

    ```

1. ## provison licenses 
    some node types requires license to run, provision license as corresponding k8s secret with "license" as the key, following is an example of provision vSIM license using default secret name:

    ```bash
    kubectl -n knl-system create secret generic vsimlic --from-file=license=<lic_file>
    ```
    check the [corresponding node type doc]({{< relref "docs/nodes/" >}}) for defautl secret name.
    
{{% /steps %}}