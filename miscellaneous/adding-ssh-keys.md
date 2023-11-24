# Adding SSH Keys to Core User in OpenShift
When installing OpenShift, we define a key pair that is to be used for the **core** user on nodes. Sometimes, we might find a reason or requirement to allow multiple SSH keys to be used in order to access the nodes via SSH.

To enable this, we can add SSH keys to the **core** user via *machineconfigs* that is getting applied to the nodes.

## Creating the machineconfig
Below is an example of how we can use a *machineconfig* in order to add a SSH key to the **core** user. Multiple keys can be added if wanted.

> **NOTE:** That adding SSH keys that are able to be used in order to access the nodes will increase the possibility of an attacker gaining access to the nodes.

```yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 99-worker-ssh-users
spec:
  config:
    ignition:
      version: 3.1.0
    passwd:
      users:
      - name: core
        sshAuthorizedKeys:
        - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIfKBsETJAx8ASDm+aMkOO22A3F50fOd4yh535WdKKUmvU
          recovery@example.com
        - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICBfK5D/7KDE0ff8x8nFQW04jQd4yh53+fO5WdKKUmvU     
          admin-1.company@example.com                                                          
        - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAGCAAACBfK5D/7KDE0ff8x8nFQW04jQd4yh53+fO5WdKKUmvU
          admin-2.company@example.com 
  extensions: null
  fips: false
  kernelArguments: null
  kernelType: ""
  osImageURL: ""
```
Here we have added the public keys of the following users:
* recovery@example.com
* admin-1.company@example.com
* admin-2.company@example.com

Having applied a *machineconfig* such as this, any user with a matching private key for any of these public keys can SSH into a node using the **core** user.