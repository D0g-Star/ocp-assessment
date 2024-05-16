## Creating syslog server on bastion host
1. dnf install rsyslog -y
sudo dnf install net-tools 
 check:
 - rpm -q rsyslog
 - dnf info rsyslog --verbose
 - systemctl status rsyslog.service
2. 
- systemctl start rsyslog.service
- systemctl status rsyslog
3.  sudo vim /etc/rsyslog.conf
uncomment :
```
module(load="imtcp") # needs to be done just once
input(type="imtcp" port="514")
```
4. open firewall:
- sudo firewall-cmd  --add-port=514/tcp  --zone=public  --permanent
- sudo firewall-cmd --reload
- sudo systemctl restart rsyslog

check listener: sudo netstat -pnltu
5. check logs with: # tail /var/log/messages 
## New logs go here:
```
template RemoteLogs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs
& stop
```

sudo systemctl restart rsyslog.servic
OR:
## Create separate file for syslog alerts from OCP cluster
vi /etc/rsyslog.d/00_poc_ocp.conf

rsyslogd -N1 -f /etc/rsyslog.d/00-poc_ocp.conf
--------------
oc get pods -n openshift-logging

https://betterstack.com/community/guides/logging/how-to-configure-centralised-rsyslog-server/


$ModLoad imtcp
$InputTCPServerRun 514
# do this in FRONT of the local/regular rules
if $fromhost-ip startswith '10.107.5' then /var/log/pococp.log
& ~

# local/regular rules, like
*.* /var/log/syslog.log