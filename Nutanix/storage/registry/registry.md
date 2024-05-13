## Image Registry 
1. Enable isci on all nodes

2. add in configuration via CLI with patch or edit:

    2.1 Edit image-registry-operator
    ```
    oc edit configs.imageregistry.operator.openshift.io
     ...
    storage:
    pvc:
        claim: 
    ...
    ```
    2.2 Patch image-registry-operator
    ```
    oc patch configs.imageregistry.operator.openshift.io cluster --patch '{"spec":{"storage":{"pvc":"image-registry-storage"{"claim":}}}}'
    ```
3. Patch operator to Managed State
```
oc patch configs.imageregistry.operator.openshift.io cluster --type merge --patch '{"spec":{"managementState":"Managed"}}'
```
4.  Check the clusteroperator status:
```
oc get clusteroperator image-registry
```
5. Create PVC Resources for openshift-image-registry namespace
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
    name: image-registry-storage
    namespace: openshift-image-registry
spec:
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 100Gi
    storageClassName: <change-this>
```

## References:
- https://access.redhat.com/documentation/en-us/assisted_installer_for_openshift_container_platform/2023/html/assisted_installer_for_openshift_container_platform/assembly_installing-on-nutanix#nutanix-post-installation-configuration_assembly_installing-on-nutanix