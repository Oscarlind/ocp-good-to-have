# Configuration
When configuring a LDAP/AD identity provider there are a few configuration parameters that are good to understand. I will be going through them below. The full example file can be found under **templates/oauth.yaml**

## The oauth configuration
In the Oauth configuration we specify a list of identity providers and how authentication from the cluster to the identity provider is happening but also how user access to the cluster should look like

```yaml
bindDN: cn=ldap-service-account,ou=ServiceAccounts,dc=mycompany,dc=com
bindPassword:
  name: ldap-secret
ca:
  name: ldap-ca-config-map

```
This configuration is how we select the service account in our LDAP/AD that we are going to use for the authentication to the LDAP/AD server itself. It also specifies where the password for the SA is located and any certificates needed to perform a secure connection.

### Limiting authentication with a proper URL
```yaml
      url: ldaps://ldap.example.com:636/dc=mycompany,dc=com?sAMAccountName?sub?(&(objectClass=user)(|(memberof=CN=ocp-admins,OU=Groups,OU=Departments,DC=mycompany,DC=com)(memberof=CN=dev-team01,OU=Groups,OU=Departments,DC=mycompany,DC=com)(memberof=CN=dev-team02,OU=Groups,OU=Departments,DC=mycompany,DC=com)(memberof=CN=platform-support,OU=Groups,OU=Departments,DC=mycompany,DC=com)))
      mappingMethod: claim
      name: AD
      type: LDAP
```
* **url:** The URL to the LDAP/AD server. This URL includes a list of specific groups, allowing only members of these groups to authenticate to the cluster.
* **mappingMethod:** Controls how mappings are established between this provider’s identities and User objects.
* **name:** The name of the identity provider (IDP). This name is displayed when logging into OpenShift via the GUI.
* **type:** The type of identity provider, indicating that LDAP is used for authentication.

In this example we are specyfing a few different things. First is the URL to the LDAP/AD server itself. This URL includes a list of a four (4) different specific groups. namely: 
* **ocp-admins** 
* **dev-team01** 
* **dev-team02** 
* **platform-support**

This means that only members of these four groups will be able to authenticate to the cluster. This is a good way of limiting access to the cluster itself.