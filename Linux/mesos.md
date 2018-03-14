# MESOS

## INSTALL

```shell
wget http://repos.mesosphere.com/el/7/x86_64/RPMS/mesos-1.0.0-2.0.89.centos701406.x86_64.rpm
sudo yum install mesos-1.0.0-2.0.89.centos701406.x86_64.rpm
mkdir -p /etc/Mesos-master
echo in_memory | sudo dd of=/etc/Mesos-master/registry

```


## INSTALL Deimos

```shell
pip install deimos
```