原文地址：http://projects.unbit.it/uwsgi/wiki/Doc
uWSGI参考资料（1.0版本的配置选项列表）

下面的内容包含了大部分uWSGI的配置选项，这些配置选项的列举没有特定的顺序。每一个选项都可以使用在任何一种支持的配置方式里（如命令行参数、环境变量、xml文件、ini文件、yaml格式文件以及LDAP）。有些选项的使用需要某些插件的支持，这些插件的名字都会在文档里有说明。

文档中的例子使用了多种形式的配置方式来让使用者更好的理解uwsgi的工作方式。

如果你刚开始接触uWSGI，你最好是先看一下快速开始 和 例子 ，这些对实际使用过程的中的一些选项做了举例说明，另外下面列举的非常多的配置选项说明对于新手的阅读可能有一点吃力。

配置风格的简单介绍
当使用某一种配置风格或者将一种风格转换另一个风格时，需要注意一下规则：

命令行参数（command line args）：需要给选项增加“--”前缀

例如socket选项：

--socket <path/address>
环境变量（environment variable）：选项名都要换成大写，并且加上“UWSGI_”前缀，所有原来选项名中的“-”都要换成下划线“_”

例如max-vars选项将变成：

UWSGI_MAX_VARS="<n>"
xml文件：xml文件中的根结点应该是<uwsgi>，所有的选项值是作为文本节点。标识符类型的选项可以没有对应的值。

socket选项和master选项可以如下配置：

<uwsgi>
    <socket>127.0.0.1:3031</socket>
    <master/>
</uwsgi>
ini文件：配置域应该是uwsgi，标识符类型的选项的值可以设为true或者1。

socket选项和master选项可以如下配置：

[uwsgi]
socket = 127.0.0.1:3031
master = true
yaml格式文件：根元素需要设置为uwsgi，标识符类型的选项的值可以设为true或者1。

socket选项和master选项可以如下配置：

uwsgi:
  socket: 127.0.0.1
  master: 1
lda格式：这个格式比较复杂，你应该查阅专门的wiki文档。见useLDAP。

无尽的选项列表...
深呼吸，现在我们开始。

socket or uwsgi-socket

指定uwsgi的客户端将要连接的socket的路径（使用UNIX socket的情况）或者地址（使用网络地址的情况）。你最多可以同时指定8个socket选项。当使用命令行变量时，可以使用“-s”这个缩写。

--socket /tmp/uwsgi.sock
以上配置将会绑定到 /tmp/uwsgi.sock 指定的UNIX socket

-s 127.0.0.1:1717
以上配置会绑定到ipv4地址127.0.0.1的1717端口

[uwsgi]
socket = 127.0.0.1:1717
socket = 127.0.0.1:2626
以上配置会绑定到ipv4地址127.0.0.1的1717端口以及ipv4地址127.0.0.1的2626端口。

protocol

设置默认的通信协议（uwsgi，http，fastcgi）

--protocol <protocol>
processes or workers

为预先派生模式设置工作进程的数量。这个设置是你的app能实现简单并且安全的并发能力的基础。你设置的工作进程越多，你就能越快的处理请求。每一个工作进程都等同于一个系统进程，它消耗内存，所以需要小心设置工作进程的数量。如果你设置的数量太多，就有可能是系统崩溃。

当你使用命令行参数时，你可以使用简化命令“-p”

--processes 8
以上配置会产生8个工作进程

--workers 4
以上配置会产生4个工作进程

-p 8 
以上会产生8个工作进程

<uwsgi>
    <workers>3</workers>
</uwsgi>
这个配置会产生3个工作进程

harakiri

这个选项会设置harakiri超时时间（可以看wiki首页的相关内容）。如果一个请求花费的时间超过了这个harakiri超时时间，那么这个请求都会被丢弃，并且当前处理这个请求的工作进程会被回收再利用（即重启）。

--harakiri 60
这个设置会使uwsgi丢弃所有需要60秒才能处理完成的请求。

harakiri-verbose

When a request is killed by harakiri you will get a message in the uWSGI log. Enabling this option will print additional info (for example in Linux will be reported the current syscall)

当一个请求被harakiri杀掉以后，你将在uWSGI日志中得到一条消息。激活这个选项会打印出额外的信息（例如，在linux中会打印出当前的syscall）。

--harakiri-verbose
以上配置会开启harakiri的额外信息。

spooler-harakiri

set the harakiri mode for spooler tasks

为spooler任务设置harakiri模式

--spooler-harakiri <n> option
mule-harakiri

为mule进程设置harakiri模式

--mule-harakiri <n>
xmlconfig or xml

加载指定的xml配置文件。当使用命令行参数时，可以使用简化命令“-x”。在xml配置文件中，你可以有多个“<uwsgi>”节，不同的节之间用id属性区分。通过在文件名后面增加id（使用冒号分隔）来选择应用哪个“<uwsgi>”节。

--xml /etc/myapp.xml
以上配置会加载/etc/myapp.xml这个配置文件。

--xml /etc/myapp.xml:django
以上命令会使用/etc/myapp.xml这个配置文件中的“django”这个节作为配置选项

这个文件内容可以像如下这样：

复制代码
<all_the_apps>

  <uwsgi id="turbogears">
    <socket>/tmp/tg.sock</socket>
  </uwsgi>

  <uwsgi id="django">
    <socket>/tmp/django.sock>
  </uwsgi>

</all_the_apps>
复制代码
这种情况下，根节点可以是任何你想要的名字（这就允许你可以将uwsgi这个配置节加到其他xml文件中）

如果在命令行的最后一个参数以“.xml”结尾，那么就隐含将加载该xml文件作为配置。

/usr/bin/uwsgi /etc/myapp.xml
以上命令会使uWSGI自动加载 /etc/myapp.xml配置文件。

set

设置一个占位符

--set KEY=VALUE
daemonize

使进程在后台运行，并将日志打到指定的日志文件或者udp服务器

--daemonize /var/log/uwsgi.log
这个指令会让uWSGI在后台运行并将日志打到 /var/log/uwsgi.log文件中。

[uwsgi]
daemonize = 192.168.0.100:4000
这个配置将会使uWSGI在后台运行，并且将日志消息发送给监听192.168.0.100：4000这个地址的udp服务器。见UdpLogging。

stop

发送一个SIGINT信号给<pidfile>文件中的pid标识的uWSGI

--stop <pidfile>
reload

发送一个SIGHUP信号给<pidfile>文件中的pid标识的uWSGI

--reload <pidfile>
listen

设置socket的监听队列大小（默认：100）。

每一个socket都有一个相关联的队列，请求会被放入其中等待进程来处理。当这个队列慢的时候，新来的请求就会被拒绝。

队列大小的最大值依赖于系统内核。

max-vars

设置uwsgi客户端能够传递给uwsgi的变量的最大数量值。这只是一个安全相关的值，大多数情况下你是不需要设置它的。

buffer-size

设置用于uwsgi包解析的内部缓存区大小。默认是4k。

如果你打算接受一个拥有很多请求头的大请求，你可以增加这个值到64k。

--buffer-size 32768
这个命令会允许uWSGI服务器接收最大为32k的uwsgi包，再大的包就会被拒绝。

memory-report

enable memory usage report. This will print in the request log information about RSS and address space usage.

开启内存使用情况报告。这将打印请求相关的内存和虚拟内存的使用情况。

<uwsgi>
    <memory-report/>
</uwsgi>
cgi-mode

开启cgi模式。响应将不再是HTTP可用的响应，而是cgi响应（会增加Status：这个请求头）

abstract-socket

chmod-socket or chmod

unix socket是个文件，所以会受到unix系统的权限限制。如果你的uwsgi客户端没有权限访问uWSGI socket，你可以用这个选项设置unix socket的权限。

当在xml配置文件中只是用这个选项作为一个标识符，那么会将权限设为666，否则就是设置为指定的权限值。

<uwsgi>
    <chmod-socket/>
</uwsgi>
这个配置会将socket文件的权限设为666

<uwsgi>
    <chmod-socket>644</chmod-socket>
</uwsgi>
这个配置会将socket文件的权限设为644

freebind (Linux only)

允许绑定到一个不存在的网络地址。

map-socket

当你将一个uWSGI实例绑定到多个socket，你可以指定某些工作进程到某些socket来提高服务质量。

复制代码
[uwsgi]
socket = /tmp/uwsgi0.sock
socket = /tmp/uwsgi1.sock
workers = 5
map-socket = 0:1,2,3
map-socket = 1:4,5
复制代码
这个配置会使工作进程1,2和3绑定到第一个socket，而工作进程4和5绑定到第二个socket。

如果你让多个app都由同一个uWSGI实例来处理，你可以很方便地为每一个app分配资源。

enable-threads

允许用内嵌的语言启动线程。这将允许你在app程序中产生一个子线程。

single-interpreter

一些语言（比如python）有“多解释器”的概念。他们允许在同一个进程中独立存在不同的app。如果你不想用这个特性，你可以使用该选项。

auto-procname

这个选项将自动给uWSGI的进程设置一些有意义的名字，例如“uWSGI master”， “uWSGI worker 1”， “uWSGI worker 2”。

procname-prefix

这个选项为进程名指定前缀。

--procname-prefix <value>
例如：

--procname-prefix test
那么，进程名将变成“testuWSGI master”、“testuWSGI worker 1”、“testuWSGI worker 2”等，test与uWSGI之间是连在一起的，可读性较差。

procname-prefix-spaced

用这个选项给进程名指定前缀时，前缀和进程名之间有空格分隔。

--procname-prefix-spaced <value>
例如：

--procname-prefix-spaced test
那么，进程名将变成“test uWSGI master”、“test uWSGI worker 1”、“test uWSGI worker 2”等，比procname-prefix的可读性好一点。

多次使用，后一次使用将覆盖前一次，即以最后一次使用为准。

procname-append

这个选项为进程名增加指定的后缀。

--procname-append <value>
例如：

--procname-append test
那么，进程名将变成“uWSGI mastertest”、“uWSGI worker 1test”、“uWSGI worker 2test”等，test与master或者1、2是连在一起的，可读性也比较差。

多次使用，后一次使用将覆盖前一次，即以最后一次使用为准。

procname

为进程指定名字。

--procname <value>
例如：

--procname test
那么，所有进程的名字（包括主进程和工作进程）都变成了“test”。只能根据PPID来判断哪个是主进程（主进程的PPID为1）。

注意，使用procname-prefix、procname-prefix-spaced以及procname-append都能在当前选项修改生效的基础上增加前缀和后缀。

多次使用，后一次使用将覆盖前一次，即以最后一次使用为准。

procname-master

指定主进程的名字。

--procname-master <value>
例如：

--procname-master test
那么，主进程的名字就变成了“test”。
注意，使用procname-prefix、procname-prefix-spaced以及procname-append都能在当前选项修改生效的基础上增加前缀和后缀。

多次使用，后一次使用将覆盖前一次，即以最后一次使用为准。

另外，该选项将覆盖procname对主进程名字的修改。所有可以把procname和procname-master配合使用，达到修改所有进程的名字的同时又能将主进程和工作进程区分开的效果。

master

启动主进程。

emperor

开启uWSGI的Emperor模式。

emperor-tyrant

开启emperor的tyrant模式。见tyrant。

emperor-stats or emperor-stats-server

为emperor模式开启一个uWSGI的统计服务器。见stats server。

--emperor-stats <addr>
early-emperor

start the emperor before jailing and privileges drop

emperor-broodlord

为emperor开启bloodlord模式。见broodlord。

emperor-amqp-vhost

set virtualhost name in AMQP emperor mode

emperor-amqp-username

set username name in AMQP emperor mode

emperor-amqp-password

set password name in AMQP emperor mode

emperor-throttle

set the number of milliseconds (default 1000) to wait before each vassal's fork()

vassals-start-hook

<filename> will be executed when the emperor starts the vassals

--vassals-start-hook <filename>
vassals-stop-hook

<filename> will be executed when the emperor stop the vassals

--vassals-stop-hook <filename>
auto-snapshot

UNKNOWN

--auto-snapshot 1
reload-mercy

设置在平滑的重启（直到接收到的请求处理完才重启）一个工作子进程中，等待这个工作结束的最长秒数。

--reload-mercy 8
这个配置会使在平滑地重启工作子进程中，如果工作进程结束时间超过了8秒就会被强行结束（忽略之前已经接收到的请求而直接结束）。

exit-on-reload

迫使在重启过程中结束uWSGI的栈。这个选项只在某系特殊情况有用。

help or usage

打印帮助信息到标准输出，然后退出。

reaper

开启reaper模式。没处理一个请求，服务器就会调用waitpid(-1)来清除所有的僵尸进程。如果你在你的app中生成了子进程，结束后又成为了很多僵尸进程，那么你可以开启这个选项。但遇到这种情况你更应该修复你这种子进程的使用方式（如果可以的话）。

max-requests

为每个工作进程设置请求数的上限。当一个工作进程处理的请求数达到这个值，那么该工作进程就会被回收重用（重启）。你可以使用这个选项来默默地对抗内存泄漏（尽管这类情况使用reload-on-as和reload-on-rss选项更有用）。

[uwsgi]
max-requests = 1000
上述配置设置工作进程没处理1000个请求就会被回收重用。

socket-timeout

为所有的socket操作设置内部超时时间（默认4秒）。

--socket-timeout 10
这个配置会结束那些处于不活动状态超过10秒的连接。

locks

create <n> locks for you to use. see locks

创建n个锁以供使用。见locks。

--locks <n>
sharedarea

这个选项将开启SharedArea。这将允许一个比较低级的内存共享。如果你希望用一个更好用更友好的共享系统，可以看CachingFramework。

--sharedarea 10
这个配置将创建一个10个页的共享内存区。

cache

开启共享cache。见CachingFramework。

cache-blocksize

设置cache的块大小，默认为65536字节。最好设置为4096的倍数。

cache-store

开启该选项，使uWSGI的cache中的内容能够被长久的保存。

cache-store-sync

设置msync()这个函数的调用频率，调用msync()这个函数能够将cache中的内容写到磁盘上。

queue

UNDOCUMENTED

queue-blocksize

UNDOCUMENTED

queue-store

UNDOCUMENTED

queue-store-sync

UNDOCUMENTED

spooler

在指定的目录下建立一个Spooler。

[uwsgi]
spooler = /home/foo/spooler
这个配置会将spooler文件保存到/home/foo/spooler directory目录下。

spooler-chdir

这个选项允许你为每一个spooler任务定义一个公共的目录。

--spooler-chdir <directory>
mule

增加一个mule进程。见Mules。

disable-logging

不记录请求信息的日志。只记录错误以及uWSGI内部消息到日志中。

pidfile

在失去权限前，将pid写到指定的pidfile文件中。

pidfile2

在失去权限后，将pid写到指定的pidfile文件中。

chroot

使用chroot（）改变默认目录到指定目录。

gid

在uWSGI服务器将要运行的情况下，设置gid。

uid

在uWSGI服务器将要运行的情况下，设置uid。

ini

设置ini配置文件的路径。

--ini <inifile>
yaml or yml

设置yaml配置文件的路径。

--yaml <yamlfile>
json

设置json格式的配置文件的路径。

格式遵循的规则跟其他支持的配置格式一样（支持正整数，布尔数和数组）：

复制代码
{

"uwsgi":  {
       "http": ":8080",
       "master": true,
       "module": "werkzeug.testapp:test_app",
       "workers": 8,
       "pythonpath":  [ "/foo", "/bar" ]
      }

} 
复制代码
为了使用JSON，你需要jansson库。默认情况下，会自动检测到库的所在位置，但是你也可以buildconf或者默认ini配置文件来指定。emperor已经被扩展支持.js文件了。

--json <jsonfile>
ldap

从ldap服务器加载配置文件。见UseLdap（目前无文档）。

ldap-schema

dump the LDAP schema (old-style format)

ldap-schema-ldif

dump the LDAP schema in LDIF format (new openldap)

no-server

初始化uWSGI服务器，然后当初始化工作完成时立即结束（可以用来作测试）。

no-defer-accept

默认情况下（这个配置有效的情况下）uWSGI会延迟调用accept()去获取请求，直到客户端发送请求数据过来（这种方法能够提高性能和安全性）。可以使用这个选项来禁用这个特性。

limit-as

通过使用POSIX/UNIX的setrlimit()函数来限制每个uWSGI进程的虚拟内存使用数。

--limit-as 256
这个配置会限制uWSGI的进程占用虚拟内存不超过256M。如果虚拟内存已经达到256M，并继续申请虚拟内存则会使程序报内存错误，本次的http请求将返回500错误。

英文文档中还解释了address space其实就是虚拟内存。

在使用这个选项前先理解这个页面的内容:  http://en.wikipedia.org/wiki/Virtual_memory

reload-on-as

当一个工作进程的虚拟内存占用超过了限制的大小，那么该进程就会被回收重用（重启）。

--reload-on-as 128
这个配置会重启所有占用虚拟内存超过128M的工作进程。当工作进程因此重启时，本次请求的响应不会受影响，返回正常结果。

reload-on-rss

跟reload-on-as的效果类似，不过这个选项控制的是物理内存。你可以同时使用这2个选项：

uwsgi:
  reload-on-as: 128
  reload-on-rss: 96
这个配置会导致所有占用128M以上虚拟内存或者超过96M物理内存的工作进程重启。

当工作进程因此重启时，本次请求的响应不会受影响，返回正常结果。

evil-reload-on-as

主进程会重启占用虚拟内存超过<n>M的工作进程。

--evil-reload-on-as <n>
evil-reload-on-rss

主要效果跟evil-reload-on-as一样，但是这个选项控制的是非共享物理内存。

--evil-reload-on-rss <n>
log-x-forwarded-for

当uWSGI运行在多代理下时，会将正确的客户端IP打到日志中。

touch-reload

当文件改变时，优雅的重启uWSGI。

uwsgi:
  touch-reload: /tmp/reload.txt
如果你使用下面的命令：

touch /tmp/reload.txt
那么uWSGI服务器就会优雅的重启。

limit-post

限制HTTP请求体的大小。uWSGI通过CONTENT_LENGTH字段值来获得请求体的大小。

--limit-post 65536
将不允许请求体超过64k的请求。

no-orphans

在没有主进程的情况下自动结束工作进程。

prio

设置进程的系统调度优先级。

<uwsgi>
    <prio>20</prio>
</uwsgi>
上述配置将设置进程的优先级为20（具体的值要根据不同的系统来设置）。
cpu-affinity

设置CPU的亲和性（只对于Linux系统）。见 http://lists.unbit.it/pipermail/uwsgi/2011-March/001594.html

post-buffering

开启http请求体的缓存。uWSGI将所有大于限定大小的HTTP请求体保存到磁盘中。

[uwsgi]
post-buffering = 8192
上述配置会使uWSGI将所有大于8K的HTTP请求体都缓存到磁盘中。Rack应用程序需要这个选项，因为Rack应用程序需要一个可回退的输入流。

post-buffering-bufsize

为post的缓存设置内部的缓冲区大小。（这个分配的内存是用来读取socket流的字节块）

post-buffering-bufsize 65536
上述配置会使uWSGI分配一个64k的socket的recv（）函数的缓存区。对一个128k的请求体来说，需要使用2个这样的缓存区。

这是一个比较高级的选项，你可能永远不会用到。

upload-progress

开启嵌入的上传进度报告。你传入一个uWSGI有写权限的目录，那么每一个上传的JSON文件都会放到该目录下，开启这个选项后，就会报告当前的上传状态。你可以使用ajax去读取这些文件，所以配置好你的web服务器允许对该目录的访问。

--upload-progress /var/www/progress
用户通过如下的url上传一个文件。

http://uwsgi.it/upload?X-Progress-ID=550e8400-e29b-41d4-a716-446655440000
uWSGI在请求url中找到X-Progress-ID，然后在 /var/www/progress下创建一个文件：

550e8400-e29b-41d4-a716-446655440000.js
该文件的内容如下：

{ "state" : "uploading", "received" : 170000, "size" : 300000 }
假设你已经在你的web服务器中将/progress这个URI映射到了/var/www/progress这个目录，那么你可以通过ajax pointing来获得那些json数据。

/progress/550e8400-e29b-41d4-a716-446655440000.js
很可能你的web服务器已经拥有一个相似的功能了，但是如果你需要改进原有的功能（或者只是想有更多的控制），那么就将该功能委托给uWSGI服务器。

no-default-app

默认情况下，当uWSGI没有找到SCRIPT_NAME指定的app时，会使用默认的app（大多数情况，默认的app都位于/目录下）。开启这个选项，在没有可用的app时会返回一个错误。

manage-script-name

如果因为某些原因，你的web服务器不能处理SCRIPT_NAME，你可以强制uWSGI自动重建PATH_INFO。

udp

启动一个udp服务器，主要用在snmp或者为UdpLogging提供一个共享的打日志服务器。

multicast

内部选项，用于第三方插件。

cluster

加入指定的集群。见Clustering。

cluster-nodes

你可以得到一个集群里的节点列表，而不需要加入这个集群。

--cluster-nodes 225.1.1.1:1717
使用上述选项能得到225.1.1.1:1717这个集群的所有节点。这个列表提供给uwsgi内部均衡负载api用的。

cluster-reload

优雅地重启整个集群。

--cluster 225.1.1.1:1717 --cluster-reload
使用上述选项会重启225.1.1.1:1717这个集群中的所有节点。

cluster-log

给一个集群的所有节点服务器发送一个日志信息。

--cluster 225.1.1.1:1717 --cluster-log "Hello World"
上述选项会在所有的节点日志文件中打印出"Hello World"

subscribe-to or st or subscribe

订阅一个SubscriptionServer，你可以通过配置多次该选项来达到订阅多个服务器的目的。

[uwsgi]
subscribe-to = 192.168.0.1:2626:unbit.it
subscribe-to = 192.168.0.2:2626:uwsgi.it
shared-socket

一个高级选项，插件作者会用到，或者用于一些特殊的需求。该选项允许服务器在启动早期创建一个socket，在失去特权或者被监禁的时候依然能使用它。

snmp

启动一个SNMP服务器。见UseSnmp

snmp-community

set the snmp community string

check-interval

主进程每秒都会进行一次扫描。如果需要你可以加长这个扫描时间。不建议。

binary-path

如果uWSGI的二进制文件路径没有加到系统环境变量中，你可以使用这个选项强制改变二进制的查找路径，那么重载系统和Emperor都能轻易找到可执行的二进制文件。

async

开启异步模式。见AsyncSupport。

logto

将日志打到一个指定的文件或者udp服务器。

logfile-chown

log-syslog

将日志打到系统日志上，而不再打到日志文件中。

传入一个参数，使uwsgi使用这个参数作为程序名，用在系统日志开始部分里。

--log-syslog mywebapp
log-master

委托主进程来负责日志的写操作（这将把所有的日志IO操作都放到一个进程中）。对使用高级的IO调度策略的系统来说是很有用的。

logdate or log-date

在每一个日志行中都打印时间信息。你可以传入一个strftime()格式的参数，来格式化时间的格式。

log-zero

打印请求大小为0的请求。

log-slow

log-4xx

log-5xx

log-big

log-sendfile

log-micros

使用微秒作为日志中响应时间的单位（默认是毫秒）。

master-as-root

以root权限运行uWSGI的主进程。

chdir

在失去权限前，使用chdir()到指定目录。

chdir2

在失去权限后，使用chdir()到指定目录。

lazy

等每一个工作进程都生成以后才加载应用程序。

cheap

延迟启动工作进程直到第一个请求到来。当第一个请求来时就会启动预先设置个数的工作进程。

如果没有设置工作进程数，那么最多只会启动一个工作进程。

cheaper

一个高阶的cheap模式，这个选项将在启动的时候只分配n个工作进程，并将使用多种算法来实现适应性的进程启动。

在启动的时候至少会分配一个工作进程，即及时设置n为0，也会在最开始启动一个工作进程。

如果当前工作进程不足以处理收到的请求，那么就会按请求量按需启动其他的工作进程，直到工作进程达到预先设置的个数。

如果没有设置工作进程数，那么只会在刚启动的时候启动一个工作进程，之后将不再启动其他进程。

--cheaper <n>
idle

在经过<secs>秒的不活跃状态后销毁工作进程（这时就进入了cheap模式），只会剩下主进程。

如果有cheaper选项，那么就进入cheaper模式，之后收到第一个请求就会启动工作进程，启动的个数由cheaper选项里配置的n决定。

--idle <secs>
mount

允许同一个进程加载多个app。

--mount /pinax=/var/www/pinax/deploy/pinax.wsgi
grunt

allows grunt processes

threads

开启线程操作模式。你必须指定每个工作进程的线程数。

--threads 40 --workers 2
这个配置会导致生成2个工作进程，每个工作进程有40个子线程。

vhost

开启虚拟主机模式。见VirtualHosting

vhost-host

默认情况下，虚拟主机模式使用SERVER_NAME作为主机名的关键字。如果你希望使用HTTP_HOST，那就加这个选项。

check-static

uWSGI会在移交控制权给指定处理者之前检查该选项指定的目录。

uWSGI会检查这个目录，如果刚好请求的PATH_INFO有一个文件在这个目录，那么uWSGI就会提供这个文件。

--check-static /var/www/example.com
使用上述配置，如果客户端请求foo.png，而该文件又恰好存在于/var/www/example.com/foo.png，那么uWSGI就会直接用指定的方法提供这个文件（默认是用sendfile ()）。

static-map

映射一个资源到静态文件区。

[uwsgi]
static-map = /media=/var/www/django/contrib/admin/media
static-map = /images=/var/www/example.com/images
 无论什么时候一个PATH_INFO匹配到配置文件中的资源，uWSGI会直接用指定的方法提供这个文件（默认是用sendfile ()）。

static-index

在目录索引中用到的静态文件的文件名。

static-index = index.html
如果请求/doc/，那么uWSGI将检查/doc/index.html，如果存在就会提供给客户端。

file-serve-mode

设置静态服务模式：

1.x-sendfile：将使用X-Sendfile头（apache, Cherokee, lighttpd）

2.x-accel-redirect：将使用X-Accel-Redirect header（nginx）

3.default：使用sendfile()

--file-serve-mode x-sendfile
check-cache

检查 uWSGI cache 中是否存在PATH_INFO指定的内容，如果存在就提供该内容。

close-on-exec

给uWSGI的socket设置close-on-exec标识，这将避免在一个请求中产生额外的进程，来继承socket文件的描述符。

mode

普通选项，app可以使用uwsgi.mode来获得这个选项值。

env

设置一个系统环境变量。

[uwsgi]
env = DJANGO_SETTINGS_MODULE=mysite.settings
这个配置将会设置一个环境变量DJANGO_SETTINGS_MODULE，它的值为mysite.settings

vacuum

当服务器退出的时候自动删除unix socket文件和pid文件。

cgroup

run the server in <group> cgroup (Linux only)

cgroup-opt

设置cgroup选项（仅限于Linux）

--cgroup-opt KEY=VAL
reuse-port (BSD only)

允许多个实例绑定到同一个地址。

loop

设置LoopEngine（高级选项）

worker-exec

funny option to map a new executable to a uWSGI worker. You can run a php fastcgi server pool in this way

/usr/bin/uwsgi --workers 4 --worker-exec /usr/bin/php53-cgi 
attach-daemon

给uWSGI主进程附加一个进程，并允许主进程控制、监控、重启这个进程。一个比较典型的使用是给主进行附加一个memcached实例。

[uwsgi]
master = true
attach-daemon = memcached
plugins

加载指定的插件。

--plugins psgi,greenlet
这个配置将加载psgi插件和greenlet插件。

allowed-modifiers

限制客户端只能访问modifiers的子集。

--allowed-modifiers 0,111
这个配置将只允许客户端访问WSGI处理程序和cache处理程序。

remap-modifier

dump-options

打印所有可用的选项，然后退出。

show-config

以ini文件格式打印当前的配置。

print

将在解析配置文件的工程中打印一个字符串。

[uwsgi]
print = foo
将在服务器启动的时候打印"foo"。

version

打印出uWSGI的版本，然后退出。

wsgi-file or file [python plugin required]

加载指定的WSGI文件（与Graham的mod_wsgi形式兼容）

eval [python plugin required]

把计算一个字符串的值作为WSGI的入口。

复制代码
<uwsgi>
  <eval>
    def application(e, sr):
    pass
  </eval>
</uwsgi>
复制代码
module [python plugin required]

加载指定的python WSGI模块（模块路径必须在PYTHONPATH里）

callable [python plugin required]

设置在收到请求时，uWSGI加载的模块中哪个变量将被调用，默认是名字为“application”的变量。

test [python plugin required]

测试一个模块是否能被成功的加载。

home or virtualenv or venv or pyhome [python plugin required]

为python程序设置指定的虚拟环境变量。

--virtualenv /apps/env001
使用在/apps/env001的虚拟环境变量。

pythonpath or python-path or pp [python plugin required]

给PYTHONPATH 增加一个目录（或者一个egg），你可以最多使用该选项64次。

[uwsgi]
pp = myapp/lib
pp = trac.egg
pymodule-alias [python plugin required]

该选项允许python模块的重映射。见PymoduleAlias。

pyargv [python plugin required]

set the python sys.argv

设置python的系统参数（sys.argv）

--pyargv "one two three"
该配置会将sys.argv 设为('one','two','three')。

optimize

设置python为最优化级别（要小心）

paste

use paste.deploy to load a WSGI app

使用paste.deploy 来加载一个WSGI程序。

uwsgi --paste config:/foo/development.ini
ini-paste

ini和paste这2个选项的简化，在文件解析完之后，将使用同样的文件作为paste.deploy的配置文件。

ini-paste-logged

load a paste.deploy config file containing uwsgi section (load loggers too)

catch-exceptions

在你的浏览器中打印出traceback信息，而不是在日志文件中（不要在实际产品中使用这个选项）。

ignore-script-name

pep3333-input

reload-os-env

no-site [python plugin required]

不加载python的site.py模块。

ping [ping plugin required]

工具性的选项。ping一个uwsgi服务器，如果ping成功，难么进程就会退出，并返回0，否则返回一个大于0的值。

/usr/bin/uwsgi --ping 192.168.0.100:1717
将ping在192.168.0.100:1717的uWSGI服务器。

ping-timeout [ping plugin required]

设置ping的超时时间（默认是3秒）。ping的等待时间超过指定的秒数后就认为该uWSGI实例已失效。

/usr/bin/uwsgi --ping 192.168.0.100:1717 --ping-timeout 10
将设置ping的超时时间为10秒。

nagios

作nagios检查。

fastrouter

Run the fastrouter (it is a uwsgi proxy/load balancer) on specific address

在指定的地址上运行fastrouter（这是一个uwsgi的代理、负载均衡）。

[uwsgi]
fastrouter = 127.0.0.1:3017
在127.0.0.1:3017这个地址上运行fastrouter

fastrouter-use-cache

check the uwsgi cache to get hostname:address mapping

fastrouter-use-pattern

use a filesystem pattern to get hostname:address mapping

fastrouter-use-base

fastrouter-events

limit the max number of async events the fastrouter can return in one cycle

fastrouter-subscription-server

add a SubscriptionServer to the fastrouter to build the hostname:address map

fastrouter-timeout

设置fastrouter的内部超时时间。

http

开启嵌入的http服务器、路由、网关、负载均衡、代理。

http-var

http-to

http-modifier1

http-use-cache

http-use-pattern

http-use-base

http-use-cluster

http-events

http-subscription-server

enable the SubscriptionServer for clustering and massive hosting/load-balancing

http-timeout

设置内部http的socket超时时间。

ugreen [ugreen plugin required]

enable uGreen as suspend/resume engine. See uGreen

ugreen-stacksize [ugreen plugin required]

set the stack size for uGreen


