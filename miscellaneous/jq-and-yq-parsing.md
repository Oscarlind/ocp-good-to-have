# General parsing
When interacting with K8s/OCP objects, especially via the CLI, it can be useful to manipulate the parsing. Below I've gathered some ways of doing that which I have found useful.

> NOTE: A lot of these examples can also be done using either **yq** or **jq**. You would, of course, need to adjust the output to be in the appropriate format **yaml** or **json** for it to work.

## Examples:
Here are some examples commands and their outputs:

## Get the decoded secret value:
Consider this secret:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-example
data:
  example: dmFsdWUtMg0KDQo=
```
In order to fetch the value from the data field, you would need to extract the value of the key `example`. To do this run the following query:

```sh
oc get secret secret -o json | jq '.data.example' -r | base64 -d
```
If the data field contains a dot, for example like this:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-example
data:
  example.dot: dmFsdWUtMg0KDQo=
```
You would need to quote the data field in the command, like this:
```sh
oc get secret secret -o json | jq '.data".example"' -r | base64 -d
```

## Get a object definition without the status field
Sometimes, I've found that I just want a quick look at the configuration of a object and not be overwhelmed with a large status field.

Consider this example of the `config.imageregistry.operator.openshift.io` 

ADD EXAMPLE:
```yaml
placeholder
```

To extract the object but skipping a field, such as **.status** we can run:
```sh
oc get configs.imageregistry.operator.openshift.io cluster -o json | jq 'del(.status)'
```
This will give us a more readable definition.