# Query every node for number of pods
Gives you a quick rundown on the # of pods running on each node in the cluster.
Using reverse **Grep** you can exclude certain pods such as **Completed** ones.
## For loop to print node name and amount of pods on the node.

```sh
for no in $(oc get no --no-headers | awk '{ print $1 }'); do echo -n "$no " ; oc get pods -A -o wide --no-headers --field-selector spec.nodeName=$no | wc -l; done
```
Alternative version that excludes Completed pods.
```sh
for no in $(oc get no --no-headers | awk '{ print $1 }'); do echo -n "$no " ; oc get pods -A -o wide --no-headers --field-selector spec.nodeName=$no | grep -v Completed | wc -l; done
```


## Example output:
```
ocp-i-01.ocp-ut.example.cluster.com 142
ocp-i-02.ocp-ut.example.cluster.com 231
ocp-i-03.ocp-ut.example.cluster.com 1044
ocp-m-01.ocp-ut.example.cluster.com 62
ocp-m-02.ocp-ut.example.cluster.com 61
ocp-m-03.ocp-ut.example.cluster.com 41
ocp-w-01.ocp-ut.example.cluster.com 95
ocp-w-02.ocp-ut.example.cluster.com 237
ocp-w-03.ocp-ut.example.cluster.com 58
ocp-w-04.ocp-ut.example.cluster.com 41
ocp-w-05.ocp-ut.example.cluster.com 44
ocp-w-06.ocp-ut.example.cluster.com 56
ocp-w-07.ocp-ut.example.cluster.com 42
ocp-w-08.ocp-ut.example.cluster.com 40
ocp-w-09.ocp-ut.example.cluster.com 67
ocp-w-10.ocp-ut.example.cluster.com 54
```

## Why?
This will give you quick feedback on the number of pods on each node. As the default limit it set on 250 (running) pods/node this can further give you information on what is happening on the cluster.

For example, in the example output above we can se a large amount of pods on ocp-i-03 (1044). This implies that there is probably a large amount of **Completed** pods running on that particular node. 

Completed pods, while not counting to the limit of how many can be present on a single node at a time, *will* have an entry in /var/log/pods on the node. 