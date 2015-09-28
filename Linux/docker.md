Docker
===

busybox
---
busybox是集成了一百多个常用linux命令和工具的软件。

``` shell
$> mkdir rootfs rootfs/etc rootfs/www
$> wget http://busybox.net/downloads/busybox-1.23.2.tar.bz2
$> wget https://www.kernel.org/pub/linux/kernel/v2.6/longterm/v2.6.32/linux-2.6.32.68.tar.xz
$> make menuconfig
busybox settings => build busybox as a static library(no shared lib),
busybox settings => installation options
$> make
$> make install
$> mkdir dev proc tmp var lib root sys
$> cat << EOF  > etc/group
$> ###################### /etc/group ######################
$> root:x:0:
$> ftp:x:800:
$> nogroup:x:65534:
$> EOF

$> cat << EOF > etc/inittab
$> ::sysinit:/etc/rcS
$> tty1::askfirst:-/bin/sh --login
$> tty2::askfirst:-/bin/sh --login
$> tty3::askfirst:-/bin/sh --login
$> EOF

$> cat << EOF > etc/profile
$> #!/bin/sh
$> EOF

$> cat << EOF > etc/protocols
ip    0    IP
icmp    1    ICMP
igmp    2    IGMP
ggp    3    GGP    
ipencap    4    IP-ENCAP
st    5    ST
tcp    6    TCP
egp    8    EGP
igp    9    IGP
pup    12    PUP
udp    17    UDP
hmp    20    HMP
xns-idp    22    XNS-IDP
rdp    27    RDP
iso-tp4    29    ISO-TP4
xtp    36    XTP    
ddp    37    DDP    
idpr-cmtp 38    IDPR-CMTP
idrp    45    IDRP
rsvp    46    RSVP
gre    47    GRE    
esp    50    IPSEC-ESP
ah    51    IPSEC-AH
skip    57    SKIP
rspf    73    RSPF CPHB
vmtp    81    VMTP
eigrp    88    EIGRP
ospf    89    OSPFIGP
ax.25    93    AX.25
ipip    94    IPIP
etherip    97    ETHERIP
encap    98    ENCAP
pim    103    PIM    
ipcomp    108    IPCOMP
vrrp    112    VRRP
l2tp    115    L2TP
isis    124    ISIS
sctp    132    SCTP
fc    133    FC

$> cat << EOF > etc/rcS
#!/bin/sh
export PATH=/bin:/sbin:/usr/bin:/usr/sbin
mount -t proc none /proc
mount -t sysfs none /sys
mount -t tmpfs tmpfs /dev -o size=512k,mode=0755
echo benjamin > /proc/sys/kernel/hostname
mkdir -p /var/run /var/log /var/state /var/tmp /var/tmp /var/mnt /dev/pts /dev/shm
mount devpts /dev/pts -t devpts
echo /bin/mdev > /proc/sys/kernel/hotplug
mdev -s
ifconfig lo 127.0.0.1 up
ifconfig eth0 192.168.1.109 netmask 255.255.255.0 up
telnetd -l /bin/sh
httpd -h /www

$> cat << EOF > etc/services
ssh 22/tcp
ssh 22/udp
telnet 23/tcp
telnet 23/udp
http 80/tcp www www-http
http 80/udp www www-http
login 513/tcp
shell 514/tcp cmd

> cat << EOF > www/index.html
> <h1>Success!</h1>
> <p>Welcome to benjamin linux!</p>
> EOF

# create  initrd
> dd if=/dev/zero of=initrd bs=1024 count=8192
> mkfs.ext2 -F initrd 
> mkdir tmp
> sudo mount -o loop initrd tmp
> sudo cp -fR rootfs/* tmp/
> umount tmp
> # build linux kernel
> cp arch/x86_64/boot/bzImage rootfs/boot
> cp initrd rootfs/boot
```
