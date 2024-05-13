# Setup 4.12 OCP Logging


## 1. Install Operators

## 1. Create Namespace
```
oc create namespace openshift-logging
```

Add Label to Namespace
openshift.io/cluster-monitoring: "true"

Clear default node selector
```
oc annotate namespace openshift-logging openshift.io/node-selector=
```

## 2. Deploy

### Recommended : Move to Infrastructure nodes if avaliable 
 * Deployment
https://docs.openshift.com/container-platform/4.9/logging/cluster-logging-deploying.html

 * Capacity Requirements
https://docs.openshift.com/container-platform/4.9/logging/config/cluster-logging-storage-considerations.html#cluster-logging-storage
