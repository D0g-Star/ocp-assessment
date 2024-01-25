## Setup NTP in Machines
### 1. Get butane executable: 
```
curl https://mirror.openshift.com/pub/openshift-v4/clients/butane/v0.16.0-1/butane --output butane
```

### 2. Make buntane file executable
  ```
  chmod +x butane
  echo $PATH
  sudo mv butane $PATH
  ``` 

### 3. Create butane files and set ntp server under Storage->files->inline
Ex ) Create a 99-worker-custom.bu & 99-master-custom.bu

```
cat <<EOF | oc create -f -
variant: openshift
version: 4.12.0
metadata:
  name: 99-worker-custom
  labels:
    machineconfiguration.openshift.io/role: worker
openshift:
  kernel_arguments:
    - loglevel=7
storage:
  files:
    - path: /etc/chrony.conf
      mode: 0644
      overwrite: true
      contents:
        inline: |
          server <ntp.server> iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
EOF

```

### 4. Use butane manifest to create yaml files
```
butane 99-worker-custom.bu -o ./99-worker-custom.yaml
butane 99-master-custom.bu -o ./99-master-custom.yaml
```

### 5. Create custom machine config
```
oc create -f 99-worker-custom.yaml
oc create -f 99-master-custom.yaml
```

### references
 * https://coreos.github.io/butane/specs/