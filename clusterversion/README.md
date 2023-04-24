# Clusterversion
Information and snippets regarding clusterversions

## Current and first installed version
How to get the currently installed version of OpenShift and the version that the cluster was originally installed with.

```sh
oc get clusterversion version -o json | jq '.status.history[].version' -r | sed -e 1b -e '$!d'
```

> Limitation: the .status.history field is not limitless. In other words, if the cluster is old enough and have had enough updates it might not find the first installed version correctly.

It has been confirmed to work on ~2-3 year old clusters.

## Why?
Can be useful to know the age of the cluster you are working with. If coming to a customer who has been running OCP for some time it can help understand the context better. 