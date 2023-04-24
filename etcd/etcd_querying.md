# How to query etcd directly for resources in OCP
Examples how to query etcd for resources.
## Examples
* Query for specific resource in target namespace:

  `etcdctl  get / --prefix --keys-only | grep RESOURCE/NAMESPACE`

* Get list of resources and their amounts in the cluster:

  `etcdctl get / --prefix --keys-only | sed '/^$/d' | cut -d/ -f3 | sort | uniq -c | sort -rn`


## Why: 
Can be useful if you are having API issues and are not able to get the required information running `oc get RESOURCE -n NAMESPACE`

One issue we have encountered is that an operator gets updated and the API changes. E.g.

```bash
oc get pipelines
Error from server: conversion webhook for tekton.dev/v1alpha1, Kind=Pipeline failed: Post "https://tekton-pipelines-webhook.openshift-pipelines.svc:443/resource-conversion?timeout=30s": no endpoints available for service "tekton-pipelines-webhook"
```
This means that OCP can't fetch the resource and we need to do it differently.
One way is to go through etcd directly.

```bash
sh-4.4# etcdctl  get / --prefix --keys-only | grep pipelines/plattform
/kubernetes.io/tekton.dev/pipelines/plattform/base-container-build-tools-master-branch
/kubernetes.io/tekton.dev/pipelines/plattform/base-openshift-norm-master-branch
/kubernetes.io/tekton.dev/pipelines/plattform/base-openshift-testapp-test-app-maven-pr
/kubernetes.io/tekton.dev/pipelines/plattform/base-openshift-testapp-test-app-maven-tiny-test-branch
/kubernetes.io/tekton.dev/pipelines/plattform/base-openshift-tumbler-master-branch
/kubernetes.io/tekton.dev/pipelines/plattform/base-openshift-tumbler-pr
/kubernetes.io/tekton.dev/pipelines/plattform/base-openshift-tumbler-readme-default-values-branch
/kubernetes.io/tekton.dev/pipelines/plattform/raas-bob-2-1-3-release
/kubernetes.io/tekton.dev/pipelines/plattform/raas-bob-master-branch
/kubernetes.io/tekton.dev/pipelines/plattform/raas-bob-pr
/kubernetes.io/tekton.dev/pipelines/plattform/tinyhook-default-bootstrap
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/base-container-build-tools-master-branch
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/base-openshift-norm-master-branch
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/base-openshift-testapp-test-app-maven-pr
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/base-openshift-testapp-test-app-maven-tiny-test-branch
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/base-openshift-tumbler-master-branch
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/base-openshift-tumbler-pr
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/base-openshift-tumbler-readme-default-values-branch
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/raas-bob-2-1-3-release
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/raas-bob-master-branch
/kubernetes.io/tiny.base.brreg.no/custompipelines/plattform/raas-bob-pr
```

> NOTE you can also use other methods to directly query the API. E.g. using curl.