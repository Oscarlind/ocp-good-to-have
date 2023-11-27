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

Sometimes, often when working with certain operators - like Quay, we are not able to add nodeSelectors and tolerations directly on our deployments. As they are operator owned the operator will simply remove them. What we can do in order to work around that issue, is to set these on the project itself instead. This will trigger an mutating **admissioncontroller** to add it to the running resouce without the operator removing it.

[Read more about it here](https://docs.openshift.com/container-platform/4.14/nodes/scheduling/nodes-scheduler-node-selectors.html#nodes-scheduler-node-selectors-project_nodes-scheduler-node-selectors)

## Project nodeSelector
This is an example of how we can use it.

We can create a new project using annotations such as this:
```yaml
kind: Project
apiVersion: project.openshift.io/v1
metadata:
  name: my-project
  annotations:
    openshift.io/node-selector: 'node-role.kubernetes.io/infra='
    scheduler.alpha.kubernetes.io/defaultTolerations: >-
      [{"operator": "Exists", "effect": "NoSchedule", "key":
      "node-role.kubernetes.io/infra"}]
```

If we are working with already existing projects/namespaces we can instead edit the *namespace* resource and add the annotations:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  annotations:
    openshift.io/sa.scc.mcs: s0:c3,c2
    openshift.io/sa.scc.supplemental-groups: 1000010000/10000
    openshift.io/sa.scc.uid-range: 1000010000/10000
    openshift.io/node-selector: 'node-role.kubernetes.io/infra='
    scheduler.alpha.kubernetes.io/defaultTolerations: >-
      [{"operator": "Exists", "effect": "NoSchedule", "key":
      "node-role.kubernetes.io/infra"}]
  name: example-namespace
```
This will automatically ensure that any workload deployed in this namespace will be put on infrastructure nodes.

Doing this can be particularly useful when working on a service or management cluster where no application workload are to be running. Instead only supporting components, such as ACM, Quay etc. 

> **NOTE:** It is also possible to configure a cluster wide selector, see below

## Configuring the default scheduler
In some cases it can be frustrating to work with a lot of taints and tolerations, as it means that for every component that is planned for a specific node or group of nodes require tolerations matching the taints.

### Example Scheduler Configuration
```yaml
apiVersion: config.openshift.io/v1
kind: Scheduler
metadata:
  name: cluster
spec:
  defaultNodeSelector: node-role.kubernetes.io/worker=
  mastersSchedulable: false
  policy:
    name: ""
status: {}
```
A Scheduler configuration such as this one will have the *Scheduler* automatically place workload on the worker nodes unless the workload has its own **nodeSelectors**. This can be convenient when working with infrastructure components and wanting to avoid using **taints & tolerations**