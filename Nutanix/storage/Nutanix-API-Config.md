#  Deploying the Nutanix CSI storage driver
## Required with Assisted Installer Installation (API)

 The Nutanix Container Storage Interface (CSI) Driver for Kubernetes provides scalable and persistent storage for stateful applications. 
1.  Create a NutanixCsiStorage resource to deploy the driver: 
    ```
    cat <<EOF | oc create -f -
    apiVersion: crd.nutanix.com/v1alpha1
    kind: NutanixCsiStorage
    metadata:
    name: nutanixcsistorage
    namespace: openshift-cluster-csi-drivers
    spec: {}
    EOF
    ```

2. Create a Nutanix secret YAML file for the CSI storage driver: 
    ```
    cat <<EOF | oc create -f -
    apiVersion: v1
    kind: Secret
    metadata:
    name: ntnx-secret
    namespace: openshift-cluster-csi-drivers
    stringData:
    # prism-element-ip:prism-port:admin:password
    key: <prismelement_address:prismelement_port:prismcentral_username:prismcentral_password> 1
    EOF
    ```
3. Validating the post-installation configurations
3.1  Verify that you can create a storage class:
    ```
    cat <<EOF | oc create -f -
    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
    name: nutanix-volume
    annotations:
        storageclass.kubernetes.io/is-default-class: 'true'
    provisioner: csi.nutanix.com
    parameters:
    csi.storage.k8s.io/fstype: ext4
    csi.storage.k8s.io/provisioner-secret-namespace: openshift-cluster-csi-drivers
    csi.storage.k8s.io/provisioner-secret-name: ntnx-secret
    # Take <nutanix_storage_container> from the Nutanix configuration; for example, SelfServiceContainer. 
    storageContainer: <nutanix_storage_container>
    csi.storage.k8s.io/controller-expand-secret-name: ntnx-secret
    csi.storage.k8s.io/node-publish-secret-namespace: openshift-cluster-csi-drivers
    storageType: NutanixVolumes
    csi.storage.k8s.io/node-publish-secret-name: ntnx-secret
    csi.storage.k8s.io/controller-expand-secret-namespace: openshift-cluster-csi-drivers
    reclaimPolicy: Delete
    allowVolumeExpansion: true
    volumeBindingMode: Immediate
    EOF
    ```
3.2  Create the persistent volume claim (PVC):

    ```
    cat <<EOF | oc create -f -
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
    name: nutanix-volume-pvc
    namespace: openshift-cluster-csi-drivers
    annotations:
        volume.beta.kubernetes.io/storage-provisioner: csi.nutanix.com
        volume.kubernetes.io/storage-provisioner: csi.nutanix.com
    finalizers:
        - kubernetes.io/pvc-protection
    spec:
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
        storage: 1Gi
    storageClassName: nutanix-volume
    volumeMode: Filesystem
    EOF
    ```

3.3  Validate that the persistent volume claim (PVC) status is Bound: 
```
 oc get pvc -n openshift-cluster-csi-drivers
```

## References: 
https://access.redhat.com/documentation/en-us/assisted_installer_for_openshift_container_platform/2023/html/assisted_installer_for_openshift_container_platform/assembly_installing-on-nutanix#deploying-the-nutanix-csi-storage-driver_assembly_installing-on-nutanix