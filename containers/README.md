## Systemd in RHEL 8 based docker image

### Dockerfile

```
FROM registry.access.redhat.com/ubi8/ubi-init
RUN dnf install httpd -y
VOLUME /sys/fs/cgroup
EXPOSE 80
CMD ["/sbin/init"]
```

## OR 

### Use my Docker hub image 

```
docker run -itd --name rhel  --privileged --tmpfs /run -v /sys/fs/cgroup:/sys/fs/cgroup:ro docker.io/dockerashu/systemd:rhel8
```

### Access container and test it 

```
root@ashu-docker-vm:~# docker ps
CONTAINER ID   IMAGE                                      COMMAND            CREATED          STATUS          PORTS     NAMES
b9e7999ba930   centos/systemd                             "/usr/sbin/init"   9 minutes ago    Up 9 minutes              x1
2312f81b51a0   registry.access.redhat.com/ubi8/ubi-init   "/sbin/init"       14 minutes ago   Up 14 minutes             rhel

=======>
root@ashu-docker-vm:~# docker exec -it rhel bash 
[root@2312f81b51a0 /]# 
[root@2312f81b51a0 /]# 
[root@2312f81b51a0 /]# dnf install httpd -y
Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered with an entitlement server. You can use subscription-manager to register.

Last metadata expiration check: 0:14:07 ago on Sun Sep 24 03:58:37 2023.
Package httpd-2.4.37-56.module+el8.8.0+19808+379766d6.7.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!


[root@2312f81b51a0 /]# 
[root@2312f81b51a0 /]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2023-09-24 03:58:50 UTC; 14min ago
     Docs: man:httpd.service(8)
 Main PID: 123 (httpd)
   Status: "Running, listening on: port 80"


```
