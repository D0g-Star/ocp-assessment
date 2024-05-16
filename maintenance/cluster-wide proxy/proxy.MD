# Configure a Cluster-wide Proxy

## Create Proxy with CA Cert

### 1. Create ca-bundle
Add the X.509 PEM encoded Certs to user-ca-bundle.yaml
Full root and intermediate cert (not wildcard)

### 2. Create the Configmap
```
oc create -f user-ca-bundle.yaml
```

### 3. Add the bundle to the cluster-wide proxy

#### 3.1 Edit the openshift proxy config
```
oc edit proxy/cluster

apiVersion: config.openshift.io/v1
kind: Proxy
metadata:
  name: cluster
spec:
  trustedCA:
    name: user-ca-bundle 
```

#### 3.2 Alternative: Patch the openshift proxy config
```
oc patch proxy/cluster --type=merge --patch='{"spec":{"trustedCA":{"name":"user-ca-bundle "}}}'  --type=merge
```

## References
- https://docs.openshift.com/container-platform/4.12/security/certificates/updating-ca-bundle.html
- https://docs.openshift.com/container-platform/4.12/networking/configuring-a-custom-pki.html
