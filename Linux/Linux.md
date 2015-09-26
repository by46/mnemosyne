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
$> gpg: Can't check signature: No public key

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
