# Creating a Recovery kubeconfig

Imagine this: you start your day with a fresh cup of coffee ☕, ready to dive into work. You sit down at your computer, eager to interact with your clusters. But hold on, there's a hiccup – you can't log in. Frustrating, right? We've all been there. This is one way of bypassing OAuth in order to access a cluster.

> **NOTE:** This requires SSH access to the control plane

I've made a script for doing this as well, can be found [here](https://oscarlind.github.io/2022/06/14/Troubleshooting-broken-api.html)

## Steps to Perform

1. SSH to a master node
2. Use crictl to locate kube-apiserver
3. Exec into the pod
4. Copy the content of `/etc/kubernetes/static-pod-resources/configmaps/kube-apiserver-cert-syncer-kubeconfig/kubeconfig` in the container to the node.

    To do this, run:
    ```sh
    crictl exec <kube-apiserver> cat /etc/kubernetes/static-pod-resources/configmaps/kube-apiserver-cert-syncer-kubeconfig/kubeconfig
    ```
    This will give you the content of a kubeconfig file that we will soon be updating. Create the file on the master node. For example, in `/tmp/kubeconfig`

5. We need to update the kubeconfig file with the content from the API certificate and token. To get this run:
    ```sh
    crictl exec <kube-apiserver> cat /etc/kubernetes/static-pod-resources/secrets/localhost-recovery-client-token/ca.crt
    ```
    And run:
    ```sh
    crictl exec <kube-apiserver> cat /etc/kubernetes/static-pod-resources/secrets/localhost-recovery-client-token/token
    ```

    Now create these two files on the master node since we will be referencing them. Again, for example `/tmp/ca.crt` and `/tmp/token`

6. Update the kubeconfig file that we created in step 4 so it looks like this

    ```yaml
    apiVersion: v1
    clusters:
      - cluster:
          certificate-authority: /tmp/ca.crt # <1> - Update location
          server: https://localhost:6443
          tls-server-name: localhost-recovery
        name: loopback

    contexts:
      - context:
          cluster: loopback
          user: kube-apiserver-cert-syncer
        name: kube-apiserver-cert-syncer

    current-context: kube-apiserver-cert-syncer

    kind: Config
    preferences: {}

    users:
      - name: kube-apiserver-cert-syncer
        user:
          tokenFile: /tmp/token # <2> - Update location
    ```

7. Run `export KUBECONFIG=/tmp/kubeconfig`

Now you've got access to the cluster.

