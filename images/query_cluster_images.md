# Query cluster for images

## One liner to get number of images per registry
```sh
oc get pods -A -o 'jsonpath={range .items[*]}{range .spec.containers[*]}{.image}{"\n"}{end}'|awk 'BEGIN { FS = "/" } ; { print $1 }'|sort|uniq -c|sort -nr
```

## Output looks like:

    687 quay.io
    391 image-registry.openshift-image-registry.svc:5000
    135 registry.redhat.io
     52 quay.apps.ocp-svc.base.brreg.no
      2 gcr.io
      2 docker.io

## Why?
Depending on the requirements, having images from certain registry might not be wanted. This will give you a quick idea of how it looks in a certain cluster.

ACS and other tools can do this better along with alerting/enforcing features as well.