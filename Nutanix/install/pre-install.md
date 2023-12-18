# This Guide is for prepping the rhel9 bastion host to deploy OCP 4.12
------
## <span style="color:green"><b> 1. Create Deployment User </span></b>

<br>
Create user used to compile bootstrap files and also have key related to name of OCP instance to allow clear deliniation

```

useradd core
usermod -aG wheel core
su root
passwrd core

```
The user core password will be: <b>defaultPassword</b>

```
su - core
id
```

Validate your in wheel:  Ex: "gid=1003(core) groups=1003(core),10(wheel)"


Generate ECDSA Key 

```
ssh-keygen -t ecdsa
```
</br>

<br>

------
## <span style="color:green"><b>2. Tools for Bastion Host </span></b>

<br>
Required:
- ssh
- ssh-keygen
- bind-utils
- sudo
- git
- curl
- tcpdump

Recommended:
- openldap-clients
- tmux
- nmap

```
sudo yum install bind-utils git curl tcpdump openldap-clients tmux nmap net-tools 
```
</br>
<br>

------
## <span style="color:green"><b>3. Download Checks </span></b>

<br>
Check downloads and network access to different systems


Download files from redhat

```
[root@ocpbastion ~]# curl -I https://access.redhat.com/downloads
HTTP/2 200
server: Apache/2.4.37 (Red Hat Enterprise Linux) OpenSSL/1.1.1k
content-language: en
x-frame-options: SAMEORIGIN
x-content-type-options: nosniff
x-ua-compatible: IE=edge
link: <https://access.redhat.com/downloads>; rel="canonical",<https://access.redhat.com/node/468693>; rel="shortlink"
x-myvhost: executed
access-control-allow-origin: *.redhat.com
x-isphp: executed
content-type: text/html; charset=utf-8
cache-control: no-cache, must-revalidate
expires: Mon, 12 Jun 2023 12:15:11 GMT
date: Mon, 12 Jun 2023 12:15:11 GMT
x-rh-edge-request-id: 2a127cb8
x-rh-edge-reference-id: 0.cc386368.1686572111.2a127cb8
x-rh-edge-cache-status: Hit from child

[root@ocpbastion ~]#
```
<br>

------
## <span style="color:green"><b>4. Add NUTANIX root CA certificate to bastion host trust </span></b>

<br>

Ex:  nutananix.acme.local pull token down to ocpbastion host

From ocpbastion as user core in git folder for specific cluster.  Ex: /home/core/git/ocpprod

```
mv download.zip vcenter01_vCenter_cert.zip

unzip vcenter01_vCenter_cert.zip

```
------
## <span style="color:green"><b>5. Add NUTANIX root CA certificate to system trust </span></b>



```
sudo cp certs/lin/* /etc/pki/ca-trust/source/anchors

sudo update-ca-trust extract
```

<br>

------
## <span style="color:green"><b>6. Check time sync from local ntp </span></b>

<br>

```
[root@ocpbastion ~]# timedatectl
               Local time: Mon 2023-06-12 08:13:14 EDT
           Universal time: Mon 2023-06-12 12:13:14 UTC
                 RTC time: Mon 2023-06-12 12:13:14
                Time zone: America/New_York (EDT, -0400)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
[root@ocpbastion ~]#


```
<br>

------
## <span style="color:green"><b>7. Install chrony with the dnf command and add in preferred NTP server. </span></b>

<br>

```
sudo dnf install -y chrony
sudo vi /etc/chrony.conf

    1. Comment out existing default server.
    2. Add: server <ntp-server> iburst

sudo systemctl restart chronyd.service
systemctl is-active chronyd.service
timedatectl
```

<br>

------
## <span style="color:green"><b>8. Check DNS Cluster Records Options.  Ex: VIP should respond back IP if DNS is setup correct </span></b>

<br> 

```
[root@ocpbastion ~]# nslookup
> ^C[root@ocpbastion ~]# nslookup ocpdev.acme.local
Server:         172.16.103.241
Address:        10.10.11.XX #53

Name:   test.apps.ocpprod.acme.local
Address: 10.10.11.XX

[root@ocpbastion ~]#

```

