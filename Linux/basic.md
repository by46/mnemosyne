Linux Basic Knowledge
===

aufs
---
![rootfs](/linux/images/rootfs.png)
Aufs是一种Union FS，简单来说就是支持将不同目录挂载到同一个虚拟文件系统下，并实现了一种layer的概念。Aufs将挂载到同一虚拟文件系统下的多个目录分别设置ready-only, read-write和whiteout-able权限，对read-only目录只能读，而写操作只能实施在read-write目录上。重点在于，写操作是在read-only上的一种增量操作，不影响read-only目录。当挂载目录时要严格按照各目录之间的这种增量关系，将被增量操作的目录优于在它基础上增量操作的目录加载，待所有read-only目录加载之后，再加载read-write目录，这变形成了层次结构。

Linux加载bootfs时会先将rootfs设置成read-only，然后系统自检之后将rootfs从read-only设置read-write。

不同Linux发行版本的bootfs基本上一致，但是rootfs略微有些差别，因此不同的发行版本可以共用bootfs：

![multi-root](/linux/images/multiroot.png)