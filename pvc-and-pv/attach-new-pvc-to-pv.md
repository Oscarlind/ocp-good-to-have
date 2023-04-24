# Attach a new PVC to a PV
For various reasons, I've had to make the data in a PV accessible to a new PVC. Depending on the circumstances this can require some manual work.

Below follows an example of how we can do this.

## Example
Let's say you have a PV that is mapped to PVC. You want to create a new PVC and map it to the PV instead. Here is a simple way of doing it.

1. Check the configuration of the PersistentVolume (PV). We are primarily interested in the key below:

```yaml
...
spec
  persistentVolumeReclaimPolicy:
```
In a dynamically provisioned PV, if this is set to **reclaim** then the PV will be deleted once the attached PVC is removed. Since we want to change the PVC this wont do.

Therefore, we need to change it to **retain**. This will allow the PV to stay even without a PVC attached to it.

2. Delete the PVC that is using the PV. We can find that by checking which PVC the pod is using and then check the corresponding PV for that PVC. The PV will change its status from `Bound` to `Released` once the PVC is gone.

3. The PV will still contain information regarding the previous PVC, so before we can attach a new one we need to do a clean up.

  ```sh
  oc patch pv NAME_OF_PV --type json -p '[{ "op": "remove", "path": "/spec/claimRef" }]'
  ```
  This will remove the claimRef section of the PV.

4. Attach a new PVC to the PV and you should be able to access the data.