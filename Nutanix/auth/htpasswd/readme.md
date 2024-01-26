## Configuring an htpasswd identity provider

### 1 . Create an htpasswd file to store the user and password information.
#### 1.1 Create or update your flat file with a user name and hashed password:
    ```
    htpasswd -c -B -b </path/to/users.htpasswd> <username> <password>
    ```
#### 1.2 Create a secret to represent the htpasswd file.
```
oc create secret generic htpass-secret --from-file=htpasswd=<path_to_users.htpasswd> -n openshift-config 
```
### 2. Define an htpasswd identity provider resource that references the secret.
#### 2.1 Add the htpasswd to the list of identitiy providers for the cluster
  ```
  oc edit oauth cluster
  ``` 

Sample Format:
```
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: my_htpasswd_provider 
    mappingMethod: claim 
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpass-secret 
```
#### 3. Add the htpasswd
### references:
- https://docs.openshift.com/container-platform/4.12/authentication/identity_providers/configuring-htpasswd-identity-provider.html