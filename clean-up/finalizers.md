# Removing stuck objects
Objects, especially CRD's can get stuck in a terminating phase. This is often due to finalizers. These are naturally used in order to ensure proper clean-up of resources. 

Before forcefully removing them read more about the subject here: [The Hidden Dangers of Terminating Namespaces](https://cloud.redhat.com/blog/the-hidden-dangers-of-terminating-namespaces)

## Methods to clean up
Here are a few methods to remove the resource.

### Investigate and remove
Find the troublesome resource/s.

`oc api-resources --verbs=list --namespaced -o name | xargs -n 1 oc get --show-kind --ignore-not-found -n NAMESPACE`

This will give you the API resources still living in that namespace. You can then go ahead and look into why they are not cleaning up properly.

Looking in the **.status** field of a terminating namespace will also provide information regarding this.

### Forcefully remove finalizers
> NOTE This is akin to nuking them. They might not be properly clean up and require a bit of [etcd querying](https://github.com/Oscarlind/ocp-good-to-have/etcd/etcd_querying.md) to verify that they are gone. 
---------------------
> DO NOT DO THIS IN PRODUCTION WANTONLY

* `oc edit <resource> <resource-name> -n <namespace>` and Delete the finalizer. This can also be done on the namespace.

* `oc patch <resource> <resource-name> -p '{"metadata":{"finalizers":null}}' --type=merge`
* `oc get <resource> <resource-name> -o=json | \
jq '.metadata.finalizers = null' | kubectl apply -f -` 

## Why?
Sometimes the automated clean up process does not properly work. This can happen if there is a CRD that is not doing its clean-up job as it is supposed to. When this happens, manual intervention is needed to ensure completion.