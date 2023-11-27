# Wiping node disks
I was once working on a OCP migration from VMware to Baremetal. During this time I was often doing re-installations of a cluster. One of the things that got to me was how much more bothersome it was to clean up the disk when using physical servers instead of VM's. Buggy API's, less convinent IPMI etc. In short, it was more work than just deleting a VM.

Due to this, I found that it was possible to have my OpenShift Nodes wipe their own disks during installation. Simply by adding a machineconfig during installation. This is the YAML file.


## Wipe disk
```yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: bm-disk
spec:
  config:
    ignition:
      version: 3.2.0
    storage:
      disks:
      - device: /dev/sda
        wipe_table: true
```

> **NOTE:** As seen on the label, this is only targeting worker nodes. In this phase of the cluster installation that is any node that is not part of the control plane. This was fine since my master nodes was running on vSphere.