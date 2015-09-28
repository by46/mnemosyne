
QEMU
---
``` shell
$> wget http://wiki.qemu-project.org/download/qemu-2.4.0.1.tar.bz2

$> sudo yum -y update

$> sudo yum groupinstall "Development Tools"

$> sudo yum install glib2-devel

$> tar -xvf qemu-2.4.0.1.tar.bz2

$> cd qemu-2.4.0.1

$> ./configure

$> make

```

虚拟化
---
1. 本地虚拟化(或全虚拟化)， 管理程序实现基本的隔离单元， 使物理硬件与来宾操作系统相分离。 VMware ESX Server 采用此技术。
2. 半虚拟化，控制程序实现了管理程序的应用程序接口(API)，它将由Guest OS调用。Kernel Virtual Machine (KVM)和Xen采用此技术。
3. 仿真，通过模拟完整的硬件环境来虚拟化Guest OS。QEMU和Bochs采用此技术。

