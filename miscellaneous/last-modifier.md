
# Last modifier of resource
This is a command that will output the last modified time of a queried resource.

## Example

```
oc get <resource> <name> -n <namespace> --show-managed-fields -o jsonpath='{range .metadata.managedFields[*]}{.manager}{" did "}{.operation}{" at "}{.time}{"\n"}{end}'
```

## Example output:

```sh
kubectl-client-side-apply did Update at 2023-03-16T08:55:17Z
```

## Why?
This can be useful to determine what and who actually modified a particular resource, which in turn can help shed light over a circumstance or facilitate troubleshooting.