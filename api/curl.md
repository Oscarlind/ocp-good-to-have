# How to access the API through curl
Following is examples of how to access the OCP resources by contacting the API via curl.

## Examples
Following commands assume you have exported a token and api variable like this:
```bash
export token=$(oc whoami -t)
export api=$(oc whoami --show-server)
```

**DELETE** a pipeline resource in the test-veg namespace:

`curl -s -X DELETE -H "Authorization: Bearer $token" $api/apis/tekton.dev/v1alpha1/namespaces/test-veg/pipelines --insecure`

**GET** all service accounts in the namespace plattform and extract the names:

`curl -s -X GET -H "Authorization: Bearer $token" $api/api/v1/namespaces/plattform/serviceaccounts --insecure | jq '.items[] | .metadata.name'`
## Why
There can be reason to want/need to communicate with the API directly, and not by the oc or kubectl client. E.g. if the resource is not retrievable due to API changes (see [etcd querying](https://github.com/Oscarlind/ocp-good-to-have/etcd/etcd_querying.md))

It can also be used for various scripts while not having a dependency on a client.
## API's
List of some common API:s
Taken from [API list](https://docs.openshift.com/container-platform/3.11/rest_api/index.html)

Note that this is just a list of common API's and might not be fully up to date.


| API   |      API Group      
|----------|:--------------|
| APIService | apiregistration.k8s.io/v1 |
| AppliedClusterResourceQuota | quota.openshift.io/v1 |
| Binding | core/v1 |
| BrokerTemplateInstance | template.openshift.io/v1 |
| Build | build.openshift.io/v1 |
| BuildConfig | build.openshift.io/v1 |
| CertificateSigningRequest | certificates.k8s.io/v1beta1 |
| ClusterNetwork | network.openshift.io/v1 |
| ClusterResourceQuota | quota.openshift.io/v1 |
| ClusterRole | authorization.openshift.io/v1 |
| ClusterRole | rbac.authorization.k8s.io/v1 |
| ClusterRoleBinding | authorization.openshift.io/v1 |
| ClusterRoleBinding | rbac.authorization.k8s.io/v1 |
| ComponentStatus | core/v1 |
| ConfigMap | core/v1 |
| ControllerRevision | apps/v1 |
| CronJob | batch/v1beta1 |
| DaemonSet | apps/v1 |
| Deployment | apps/v1 |
| DeploymentConfig | apps.openshift.io/v1 |
| EgressNetworkPolicy | network.openshift.io/v1 |
| Endpoints | core/v1 |
| Event | core/v1 |
| Event | events.k8s.io/v1beta1 |
| Group | user.openshift.io/v1 |
| HorizontalPodAutoscaler | autoscaling/v1 |
| HostSubnet | network.openshift.io/v1 |
| Identity | user.openshift.io/v1 |
| Image | image.openshift.io/v1 |
| ImageSignature | image.openshift.io/v1 |
| ImageStream | image.openshift.io/v1 |
| ImageStreamImage | image.openshift.io/v1 |
| ImageStreamImport | image.openshift.io/v1 |
| ImageStreamMapping | image.openshift.io/v1 |
| ImageStreamTag | image.openshift.io/v1 |
| Job | batch/v1 |
| LimitRange | core/v1 |
| LocalResourceAccessReview | authorization.openshift.io/v1 |
| LocalSubjectAccessReview | authorization.k8s.io/v1 |
| LocalSubjectAccessReview | authorization.openshift.io/v1 |
| MutatingWebhookConfiguration | admissionregistration.k8s.io/v1beta1 |
| Namespace | core/v1 |
| NetNamespace | network.openshift.io/v1 |
| NetworkPolicy | networking.k8s.io/v1 |
| Node | core/v1 |
| OAuthAccessToken | oauth.openshift.io/v1 |
| OAuthAuthorizeToken | oauth.openshift.io/v1 |
| OAuthClient | oauth.openshift.io/v1 |
| OAuthClientAuthorization | oauth.openshift.io/v1 |
| PersistentVolume | core/v1 |
| PersistentVolumeClaim | core/v1 |
| Pod | core/v1 |
| PodDisruptionBudget | policy/v1beta1 |
| PodSecurityPolicyReview | security.openshift.io/v1 |
| PodSecurityPolicySelfSubjectReview | security.openshift.io/v1 |
| PodSecurityPolicySubjectReview | security.openshift.io/v1 |
| PodTemplate | core/v1 |
| PriorityClass | scheduling.k8s.io/v1beta1 |
| Project | project.openshift.io/v1 |
| ProjectRequest | project.openshift.io/v1 |
| RangeAllocation | security.openshift.io/v1 |
| ReplicaSet | apps/v1 |
| ReplicationController | core/v1 |
| ResourceAccessReview | authorization.openshift.io/v1 |
| ResourceQuota | core/v1 |
| Role | authorization.openshift.io/v1 |
| Role | rbac.authorization.k8s.io/v1 |
| RoleBinding | authorization.openshift.io/v1 |
| RoleBinding | rbac.authorization.k8s.io/v1 |
| RoleBindingRestriction | authorization.openshift.io/v1 |
| Route | route.openshift.io/v1 |
| Secret | core/v1 |
| SecurityContextConstraints | security.openshift.io/v1 |
| SelfSubjectAccessReview | authorization.k8s.io/v1 |
| SelfSubjectRulesReview | authorization.k8s.io/v1 |
| SelfSubjectRulesReview | authorization.openshift.io/v1 |
| Service | core/v1 |
| ServiceAccount | core/v1 |
| StatefulSet | apps/v1 |
| StorageClass | storage.k8s.io/v1 |
| SubjectAccessReview | authorization.k8s.io/v1 |
| SubjectAccessReview | authorization.openshift.io/v1 |
| SubjectRulesReview | authorization.openshift.io/v1 |
| Template | template.openshift.io/v1 |
| TemplateInstance | template.openshift.io/v1 |
| TokenReview | authentication.k8s.io/v1 |
| User | user.openshift.io/v1 |
| UserIdentityMapping | user.openshift.io/v1 |
| ValidatingWebhookConfiguration | admissionregistration.k8s.io/v1beta1 |
| VolumeAttachment | storage.k8s.io/v1beta1 |
