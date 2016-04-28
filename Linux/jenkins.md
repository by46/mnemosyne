Jenkins
=============================

Installation
---------------------------------
Add the Jenkins repository to the yum repos, and install Jenkins from here.

```shell

sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins

```

Start/Stop
---------------------

```shell
sudo service jenkins start/stop/restart
sudo chkconfig jenkins on

```