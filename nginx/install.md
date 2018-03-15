# NGINX

## install

[官网](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)

To add NGINX yum repository, create a file named /etc/yum.repos.d/nginx.repo and
paste one of the configurations below:

CentOS

```shell
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

RHEL


```shell
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/rhel/$releasever/$basearch/
gpgcheck=0
enabled=1
```

Due to differences between how CentOS, RHEL, and Scientific Linux
populate the $releasever variable, it is necessary to manually replace $releasever
with either 5 (for 5.x) or 6 (for 6.x), depending upon your OS version.