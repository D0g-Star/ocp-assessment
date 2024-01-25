## Node Maintence

### To perform a graceful restart of a node:
#### 1. Mark the node as unschedulable:
```
oc adm cordon <node1>
```
#### 2. Drain the node to remove all the running pods:
```
oc adm drain <node1> --ignore-daemonsets --delete-emptydir-data --force
```
#### 3. Access the node in debug mode or ssh into node:
##### 3.1 debug mode 
```
oc debug node/<node1>
``` 
####  3.2 ssh into node
```
ssh -i ssh-key core@<master-node>.<cluster_name>.<base_domain> 
```
#### 4. Change your root directory to /host::
```
 chroot /host
```
#### 5. Restart the node:
```
systemctl reboot
```
#### 6.
```
oc adm uncordon <node1>
```
## References:
- https://docs.openshift.com/container-platform/4.12/nodes/nodes/nodes-nodes-rebooting.html