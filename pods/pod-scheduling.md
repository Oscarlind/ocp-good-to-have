# Pod Scheduling in OpenShift

There are multiple different strategies to control pod placement in OpenShift. The links below covers them in-depth. I will also use a couple of examples on how to achieve pod placement in different scenarios.

1. [Node Selectors](https://docs.openshift.com/container-platform/latest/nodes/scheduling/nodes-scheduler-node-selectors.html)
2. [Tolerations and Taints](https://docs.openshift.com/container-platform/latest/nodes/scheduling/nodes-scheduler-taints-tolerations.html)
3. [Scheduler Configurations](https://docs.openshift.com/container-platform/4.14/nodes/scheduling/nodes-scheduler-about.html)
4. [Affinity and Anti-affinity](https://docs.openshift.com/container-platform/4.14/nodes/scheduling/nodes-scheduler-pod-affinity.html)

## Using nodeSelectors
Using **nodeSelectors** on our *Deployments* or other resources is a useful tactic in order to control the placement of our pods. This is often used together with **taints & tolerations** in order to keep certain nodes running with only specific workload. In OpenShift this is a common strategy for infrastructure nodes.

### YAML Example
```yaml
---
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  advanced:
    retentionConfig:
      retentionResolution1h: 30d
      retentionResolution5m: 14d
      retentionResolutionRaw: 5d
  nodeSelector:                      # <-- Here we define a nodeSelector to target nodes with the role infra
    node-role.kubernetes.io/infra: ''
  tolerations:                       # <-- This section adds tolerations to the pods in order to allow them to run on the nodes with these taints
  - effect: NoSchedule
    key: node-role.kubernetes.io/infra
    value: reserved
  - effect: NoExecute
    key: node-role.kubernetes.io/infra
    value: reserved
...
```