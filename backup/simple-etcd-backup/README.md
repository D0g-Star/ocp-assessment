# NFS Backup of ETCD 

## In OCP CLI :
### 1. Create a project.
    oc new-project ocp-etcd-backup --description "Openshift Backup Automation Tool" --display-name "Backup ETCD Automation"
### 2. Create Service Account
    oc apply -f sa-etcd-bkp.yaml
### 3. Create ClusterRole
    oc apply -f cluster-role-etcd-bkp.yaml
### 4.Create ClusterRoleBinding
    oc apply -f cluster-role-binding-etcd-bkp.yaml
### 5. Add service account to SCC "privileged":
    oc adm policy add-scc-to-user privileged -z openshift-backup
### 6. Create Backup CronJob
#### 6.1 Change <nfs-server-IP>:<shared-path>  as required. Also, make sure on you already have the /home/core/backup directory created.
    oc apply -f cronjob-etcd-bkp.yaml
### 7. After creating CronJob, you can force the execution for validation with the command:
    oc create job backup --from=cronjob/openshift-backup

<br>

## Create and Set up NFS RHEL Host
### 2.1 NFS Creation
```
sudo dnf install nfs-utils -y
 systemctl start nfs-server
 systemctl enable nfs-server
 systemctl status nfs-server
 sudo mkdir -p /mnt/openshift/backup
 vi /etc/exports
```
### 2.2 Add the following, including the options for rw,no_wdelay,no_root_squash:
```
/mnt/openshift/backup   192.168.0.1/24(rw,sync,no_wdelay,no_root_squash,insecure)
```
### 2.3 Export the new share with:
    exportfs -arv
### 2.4 Confirm share is visible:

    exportfs  -s
    showmount -e 127.0.0.1
### 2.5 If required, open up the firewall ports needed:
```
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --permanent --add-service=mountd
firewall-cmd --reload
```
## References:
- https://docs.openshift.com/container-platform/4.12/backup_and_restore/control_plane_backup_and_restore/backing-up-etcd.html
- https://access.redhat.com/solutions/5843611
