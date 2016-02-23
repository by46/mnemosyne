Linux
====

User
----

``` shell
sudo useradd -g root benjamin
sudo passwd benjamin

sudo vi /etc/sudoers

```

Signature file
---
for example:
download qemu-2.4.0.1.tar.bz2 and qemu-2.4.0.1.tar.bz2.sig

``` shell
gpg --verify qemu-2.4.01.tar.bz2.sig qemu-2.4.0.1.tar.bz2

$> gpg: Signature made Wed 23 Sep 2015 06:11:29 AM CST using RSA key ID F108B584
$> gpg: Can\'t check signature: No public key

gpg --recv-keys F108B584

gpg --verify --verbose qemu-2.4.01.tar.bz2.sig qemu-2.4.0.1.tar.bz2


$>
gpg: Signature made Wed 23 Sep 2015 06:11:29 AM CST using RSA key ID F108B584
gpg: using PGP trust model
gpg: Good signature from "Michael Roth <flukshun@gmail.com>"
gpg:                 aka "Michael Roth <mdroth@utexas.edu>"
gpg:                 aka "Michael Roth <mdroth@linux.vnet.ibm.com>"
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: CEAC C9E1 5534 EBAB B82D  3FA0 3353 C9CE F108 B584
gpg: binary signature, digest algorithm SHA1

```

dd
---
dd if=/dev/zero of=/tmp/tmpfile bs=1024 count=8192
convert and copy a file

/etc/fstab
---
存放文件系统静态信息的文件，当系统启动时，系统会自动读取该文件内容，并且会自动将文件中指定的文件系统挂载到指定目录。该文件中的六个域分别为：<file system>, <mount point>, <type>, <options>, <dump>, <pass>。

* file system
    - 用来指定需要挂载的文件系统的设备名称和块信息，或者是远程的文件系统
* mount point
    - 挂载点，把文件系统挂载到该目录下载。对于swap分区，该域应该设置为none，表示没有挂载点。
* type
    - 用于指定文件系统的文件类型。linux所支持的类型：adfs, befs, cifs, ext, ext2, ext3, iso9660, kafs, minix, msdos, vfat,umsdos, proc, reiserfs, swap, squashfs, nfs, hpfs, ncpfs, ntfs, nfs, ufs。具体查看系统中支持的文件系统类型可以通过cat /proc/filesystems
* options
    - 用来设置选项，用逗号分隔
* dump


grub2
---
1. ubuntu
``` shell
wget http://mirrors.ustc.edu.cn/gnu/grub/grub-2.00.tar.xz
wget http://mirrors.ustc.edu.cn/gnu/grub/grub-2.00.tar.xz.sig
gpg --verify grub-2.00.tar.xz.sig grub-2.00.tar.xz
gpg --recv-keys <id>
gpg --verify --verbose grub-2.00.tar.xz.sig grub-2.00.tar.xz

sudo apt-get install bison flex libdevmapper-dev ttf-dejavu

```

2. CentOS
``` shell
wget http://mirrors.ustc.edu.cn/gnu/grub/grub-2.00.tar.xz
wget http://mirrors.ustc.edu.cn/gnu/grub/grub-2.00.tar.xz.sig
gpg --verify grub-2.00.tar.xz.sig grub-2.00.tar.xz
gpg --recv-keys <id>
gpg --verify --verbose grub-2.00.tar.xz.sig grub-2.00.tar.xz

sudo yum install device-mapper

make


```