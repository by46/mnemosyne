CentOS 6.4
===
upload yum repository

1. backup /etc/yum.repos.d/CentOS-Base.repo to /etc/yum.repos.d/CentOS-Base.repo.bak
2. download repository file 
 * [CentOS5](http://mirrors.163.com/.help/CentOS5-Base-163.repo), 
 * [CentOS6](http://mirrors.163.com/.help/CentOS6-Base-163.repo)
3. yum makecache

CentOS 7.0
===
minimal ISO


Linux update
===
1. download kernel source code
2. make menuconfig
3. make 
4. make modules_install install
5. reboot
