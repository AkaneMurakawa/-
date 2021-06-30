# redis.conf中文翻译

## Redis配置

Redis在配置文件中，将配置根据不同功能和模块进行划分，你可以按照关键字搜索阅读指定的模块，如下形式:

```text
################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
```

## Redis模块

注：在这里整理Redis模块，有助于我们可以根据关键字快速寻找某一块的配置。

* INCLUDES 引入配置文件
* MODULES 引入模块
* NETWORK 网络配置
* TLS/SSL 安全连接
* GENERAL 通常配置
* SNAPSHOTTING 快照\(俗称的RDB\)
* REPLICATION \(主从\)复制
* KEYS TRACKING KEYS跟踪
* SECURITY 安全
* CLIENTS 客户端
* MEMORY MANAGEMENT 内存管理
* LAZY FREEING 懒释放\(阻塞和非阻塞\)
* THREADED I/O 线程I/O
* APPEND ONLY MODE 追加模式\(APPEND ONLY FILE俗称的AOF\)
* LUA SCRIPTING LUA脚本
* REDIS CLUSTER Redis集群
* CLUSTER DOCKER/NAT support  DOCKER/NAT方式集群支持
* SLOW LOG 慢查询日志
* LATENCY MONITOR 延迟监视器
* EVENT NOTIFICATION 事件通知
* GOPHER SERVER 地图服务器
* ADVANCED CONFIG 高级配置
* ACTIVE DEFRAGMENTATION 激活碎片整理

```text
# Redis configuration file example.
# Redis配置文件示例
#
# Note that in order to read the configuration file, Redis must be
# started with the file path as first argument:
# 记住如果你要读取指定的配置文件，必须将配置文件的路径作为第一个参数，例如: 
#
# ./redis-server /path/to/redis.conf

# Note on units: when memory size is needed, it is possible to specify
# it in the usual form of 1k 5GB 4M and so forth:
# 注意: 当你需要配置内存大小时，你可以使用1k 5GB 4M等类型格式的单位
#
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.
# 单位是不区分大小写的，写1GB 1Gb 1gB都是一样的
```

### INCLUDES 引入配置文件

```text
################################## INCLUDES ###################################
# Redis的引入配置文件
#
# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
# 在配置文件中，你可以引入一个或者多个其他配置文件，这样使得你的配置更清晰
#
# Notice option "include" won't be rewritten by command "CONFIG REWRITE"
# from admin or Redis Sentinel. Since Redis always uses the last processed
# line as value of a configuration directive, you'd better put includes
# at the beginning of this file to avoid overwriting config change at runtime.
# 记住，当使用"include"引入其他配置时，在admin和Redis哨兵下，使用"CONFIG REWRITE"是不会重新读取的
# 如果配置有冲突，Redis总是读取最后的配置。如果你不希望覆盖配置，你可以将 "include" 放在前面
#
# If instead you are interested in using includes to override configuration
# options, it is better to use include as the last line.
# 如果你希望覆盖配置，你可以将 "include" 放到最后一行
#
# include /path/to/local.conf
# include /path/to/other.conf
```

### MODULES 引入模块

```text
################################## MODULES #####################################
# Redis的引入模块
#
# Load modules at startup. If the server is not able to load modules
# it will abort. It is possible to use multiple loadmodule directives.
# 启动的时候会加载模块
# 作用：给Redis增加功能，更多了解请阅读：https://zhuanlan.zhihu.com/p/44685035
#
# loadmodule /path/to/my_module.so
# loadmodule /path/to/other_module.so
```

### NETWORK 网络配置

```text
################################## NETWORK #####################################
# Redis的网络配置
# 
# By default, if no "bind" configuration directive is specified, Redis listens
# for connections from all the network interfaces available on the server.
# It is possible to listen to just one or multiple selected interfaces using
# the "bind" configuration directive, followed by one or more IP addresses.
# 如果没有直接配置 "bind"，默认是监听所有可用的服务。
# 你最好配置一个或者多个 "bind" 配置，例如:
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
#
# ~~~ WARNING ~~~ If the computer running Redis is directly exposed to the
# internet, binding to all the interfaces is dangerous and will expose the
# instance to everybody on the internet. So by default we uncomment the
# following bind directive, that will force Redis to listen only into
# the IPv4 loopback interface address (this means Redis will be able to
# accept connections only from clients running into the same computer it
# is running).
# ~~~警告~~~ 如果运行Redis的计算机直接暴露于互联网，绑定到所有接口是很危险的，
# 并且会暴露实例给互联网上的所有人。因此，默认情况下，我们取消注释遵循bind指令，
# 这将强制Redis仅侦听IPv4环回接口地址
# (意味着Redis将只能够接受来自同一台计算机上运行的客户端的连接)
# 
#
# IF YOU ARE SURE YOU WANT YOUR INSTANCE TO LISTEN TO ALL THE INTERFACES
# JUST COMMENT THE FOLLOWING LINE.
# 如果你想要监听绑定所有的接口，你可以注释掉下面内容 # bind 127.0.0.1
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
bind 127.0.0.1

# 保护模式，只能本机访问
# Protected mode is a layer of security protection, in order to avoid that
# Redis instances left open on the internet are accessed and exploited.
# 保护模式只是一种安全保护法，避免Redis在互联网上被随便访问
#
# When protected mode is on and if:
#
# 1) The server is not binding explicitly to a set of addresses using the
#    "bind" directive.
# 2) No password is configured.
# 以下情况会开启保护模式:
#   1) 使用"bind"，服务没有明确指定 绑定的地址
#   2) 配置文件中未配置密码
#
# The server only accepts connections from clients connecting from the
# IPv4 and IPv6 loopback addresses 127.0.0.1 and ::1, and from Unix domain
# sockets.
# 支持IPv4 和IPv6，绑定地址为: 127.0.0.1 and ::1
#
# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured, nor a specific set of interfaces
# are explicitly listed using the "bind" directive.
# 保护模式默认是开启的，如果你想其他客户端随便访问，你可以关闭'no'
protected-mode yes

# Accept connections on the specified port, default is 6379 (IANA #815344).
# If port 0 is specified Redis will not listen on a TCP socket.
# 默认端口，如果指定port 0， Redis将不会监听TCP socket
port 6379

# TCP listen() backlog.
#
# In high requests-per-second environments you need an high backlog in order
# to avoid slow clients connections issues. Note that the Linux kernel
# will silently truncate it to the value of /proc/sys/net/core/somaxconn so
# make sure to raise both the value of somaxconn and tcp_max_syn_backlog
# in order to get the desired effect.
# tcp-backlog: 此参数确定了TCP连接中已完成队列(完成三次握手之后)的长度，默认是511。
# 但是不能 大于 Linux系统定义的/proc/sys/net/core/somaxconn值(somaxconn 默认128)
# 当系统并发量大并且客户端速度缓慢的时候，可以将这二个参数一起参考设定，以解决问题。

tcp-backlog 511

# Unix socket.
#
# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
# 指定redis监听的unix socket路径，默认不启用，unixsocketper指定文件的权限
# unixsocket /tmp/redis.sock
# unixsocketperm 700

# Close the connection after a client is idle for N seconds (0 to disable)
# 连接超时(设置为0 则不限制超时)
timeout 0

# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
# 如果超时时间非0，则发送TCP ACKs包检测客户端连接是否存活，这有两个好处:
#
# 1) Detect dead peers.
# 2) Take the connection alive from the point of view of network
#    equipment in the middle.
# 1) 检测死亡连接
# 2) 查看网络中活跃连接
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
#
# A reasonable value for this option is 300 seconds, which is the new
# Redis default starting with Redis 3.2.1.
# 指定在一个 client 空闲多少秒之后关闭连接(0表示永不关闭)
# 从Redis 3.2.1.开始，官方建议是300s
tcp-keepalive 300
```

### TLS/SSL 安全连接

```text
################################# TLS/SSL #####################################
# TLS/SSL
# 
# By default, TLS/SSL is disabled. To enable it, the "tls-port" configuration
# directive can be used to define TLS-listening ports. To enable TLS on the
# default port, use:
#
# port 0
# tls-port 6379

# Configure a X.509 certificate and private key to use for authenticating the
# server to connected clients, masters or cluster peers.  These files should be
# PEM formatted.
#
# tls-cert-file redis.crt 
# tls-key-file redis.key

# Configure a DH parameters file to enable Diffie-Hellman (DH) key exchange:
#
# tls-dh-params-file redis.dh

# Configure a CA certificate(s) bundle or directory to authenticate TLS/SSL
# clients and peers.  Redis requires an explicit configuration of at least one
# of these, and will not implicitly use the system wide configuration.
#
# tls-ca-cert-file ca.crt
# tls-ca-cert-dir /etc/ssl/certs

# By default, clients (including replica servers) on a TLS port are required
# to authenticate using valid client side certificates.
#
# It is possible to disable authentication using this directive.
#
# tls-auth-clients no

# By default, a Redis replica does not attempt to establish a TLS connection
# with its master.
#
# Use the following directive to enable TLS on replication links.
#
# tls-replication yes

# By default, the Redis Cluster bus uses a plain TCP connection. To enable
# TLS for the bus protocol, use the following directive:
#
# tls-cluster yes

# Explicitly specify TLS versions to support. Allowed values are case insensitive
# and include "TLSv1", "TLSv1.1", "TLSv1.2", "TLSv1.3" (OpenSSL >= 1.1.1) 
#
# tls-protocols TLSv1.2

# Configure allowed ciphers.  See the ciphers(1ssl) manpage for more information
# about the syntax of this string.
#
# Note: this configuration applies only to <= TLSv1.2.
#
# tls-ciphers DEFAULT:!MEDIUM

# Configure allowed TLSv1.3 ciphersuites.  See the ciphers(1ssl) manpage for more
# information about the syntax of this string, and specifically for TLSv1.3
# ciphersuites.
#
# tls-ciphersuites TLS_CHACHA20_POLY1305_SHA256

# When choosing a cipher, use the server's preference instead of the client
# preference. By default, the server follows the client's preference.
#
# tls-prefer-server-ciphers yes
```

### GENERAL 通常配置

```text
################################# GENERAL #####################################
# 通常配置
# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
# Redis默认是不会后台运行的，如果需要请设置为 'yes'
# 记住如果是后台运行，pid会被写入到 /var/run/redis.pid (可自己配置路径)
daemonize no

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
# 可以通过upstart和systemd管理Redis守护进程
#   supervised no - 没有监督互动
#   supervised upstart - 通过将Redis置于SIGSTOP模式来启动信号
#   supervised systemd - signal systemd将READY = 1写入$ NOTIFY_SOCKET
#   supervised auto - 检测upstart或systemd方法基于 UPSTART_JOB或NOTIFY_SOCKET环境变量
supervised no

# If a pid file is specified, Redis writes it where specified at startup
# and removes it at exit.
#
# When the server runs non daemonized, no pid file is created if none is
# specified in the configuration. When the server is daemonized, the pid file
# is used even if not specified, defaulting to "/var/run/redis.pid".
# 当redis服务以非守护进程运行时，pid文件不会创建。当以守护线程运行时，pid默认写到"/var/run/redis.pid"
#
# Creating a pid file is best effort: if Redis is not able to create it
# nothing bad happens, the server will start and run normally.
# 配置 pid 文件路径，当redis作为守护进程运行的时候，
# 会把 pid 默认写到 /var/redis/run/redis_6379.pid 文件里面
pidfile /var/run/redis_6379.pid

# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
# 日志级别:
#   debug (记录大量日志信息，适用于开发、测试环境)
#   verbose (verbose：冗长的)(没有debug级别那么多的日志)
#   notice (适当融创，适用于生产环境)
#   warning (只记录重要 / 关键信息)
loglevel notice

# Specify the log file name. Also the empty string can be used to force
# Redis to log on the standard output. Note that if you use standard
# output for logging but daemonize, logs will be sent to /dev/null
# 日志文件的位置，当指定为空字符串时，为标准输出，如果redis已守护进程模式运行，那么日志将会输出到/dev/null
logfile ""

# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# 要把日志记录到系统日志，将'syslog-enabled' 设置为 'yes'
# syslog-enabled no

# Specify the syslog identity.
# 设置syslog的标识
# syslog-ident redis

# Specify the syslog facility. Must be USER or between LOCAL0-LOCAL7.
# 指定系统日志设置，必须是 USER 或者是 LOCAL0-LOCAL7 之间的值
# syslog-facility local0

# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
# 设置数据库大小，默认是0，你可以使用SELECT <dbid数据库下标> 切换数据库。默认是从0开始
databases 16

# By default Redis shows an ASCII art logo only when started to log to the
# standard output and if the standard output is a TTY. Basically this means
# that normally a logo is displayed only in interactive sessions.
# 默认情况下，Redis仅在开始登录时才显示ASCII logo，标准输出。
# 如果是TTY模式的标准输出，通过只在交互中显示。
#
# However it is possible to force the pre-4.0 behavior and always show a
# ASCII art logo in startup logs by setting the following option to yes.
#
# 是否总是显示logo
always-show-logo yes
```

### SNAPSHOTTING 快照\(俗称的RDB\)

```text
################################ SNAPSHOTTING  ################################
# 快照存储(俗称的RDB)
# Save the DB on disk:
# 存储快照到磁盘
#
# 译者注: replica(复制的意思，这里翻译为slave)
#
#   save <seconds> <changes>
#   格式: save <间隔时间(s)> <写入次数> 
#   写入次数指的是keys changed
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#   通过设置 间隔时间 和 写入次数 来告诉redis说明时候将DB数据存储到磁盘
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#   下面的例子说明:
#       save 900 1  900/60 = 15min，15分钟内，有1个key改变，则存储
#       save 300 10 300/60 = 5min，5分钟内，有10个key改变，则存储
#       save 60 10000  60/60 = 1min，1分钟内，有10000个key改变，则存储
#       # 不得不佩服Redis的设计
# 
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#   你可以注释掉所有 "save"停用该功能，也可以使用save ""表示停用
#
#   save ""

save 900 1
save 300 10
save 60 10000

# By default Redis will stop accepting writes if RDB snapshots are enabled
# (at least one save point) and the latest background save failed.
# This will make the user aware (in a hard way) that data is not persisting
# on disk properly, otherwise chances are that no one will notice and some
# disaster will happen.
# 默认情况下，Redis是启用了RDB快照，如果在持久化数据到磁盘时失败了，Redis将会停止写入(至少一个保存点)，
# 这样的好处是，让用户注意到(很难)数据没有正确持久化(persisting)到磁盘，
# 否则没人注意到这个问题，可能会发生灾难(disaster)性的后果。
#
# If the background saving process will start working again Redis will
# automatically allow writes again.
# 如果 后台保存程序 启动并再次运行工作了，REdis将会自动运行写入
#
# However if you have setup your proper monitoring of the Redis server
# and persistence, you may want to disable this feature so that Redis will
# continue to work as usual even if there are problems with disk,
# permissions, and so forth.
# 你可以设置当持久化出问题时，是否要停止写入
stop-writes-on-bgsave-error yes

# Compress string objects using LZF when dump .rdb databases?
# For default that's set to 'yes' as it's almost always a win.
# If you want to save some CPU in the saving child set it to 'no' but
# the dataset will likely be bigger if you have compressible values or keys.
# 是否需要使用LZF压缩算法 压缩dump .rdb?
# 默认是 'yes'，一直开启的
# 如果你不希望消耗CPU进行压缩，你可以设置为 'no'，但是这样文件将会变大。
rdbcompression yes

# Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
# This makes the format more resistant to corruption but there is a performance
# hit to pay (around 10%) when saving and loading RDB files, so you can disable it
# for maximum performances.
# 从RDB版本5开始，可以使用CRC64校验算法进行数据校验，但是这样将增加大约 10% 的CPU消耗
# 如果你想性能最大提升，可以关闭它
#
# RDB files created with checksum disabled have a checksum of zero that will
# tell the loading code to skip the check.
# 当启动校验时，如果校验结果为0，意味着告诉Redis加载代码时跳过检测
rdbchecksum yes

# The filename where to dump the DB
# 设置快照文件名
dbfilename dump.rdb

# Remove RDB files used by replication in instances without persistence
# enabled. By default this option is disabled, however there are environments
# where for regulations or other security concerns, RDB files persisted on
# disk by masters in order to feed replicas, or stored on disk by replicas
# in order to load them for the initial synchronization, should be deleted
# ASAP. Note that this option ONLY WORKS in instances that have both AOF
# and RDB persistence disabled, otherwise is completely ignored.
#
# An alternative (and sometimes better) way to obtain the same effect is
# to use diskless replication on both master and replicas instances. However
# in the case of replicas, diskless is not always an option.
# RDB文件删除时是否加同步锁
rdb-del-sync-files no

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
# AOF文件也会创建在这个目录
#
# Note that you must specify a directory here, not a file name.
# 设置快照文件 路径，这必须是文件目录，不能是文件名
dir ./
```

### REPLICATION \(主从\)复制

```text
################################# REPLICATION #################################
# 主从复制(主: master 从: slave 复制: replication)
#
# Master-Replica replication. Use replicaof to make a Redis instance a copy of
# another Redis server. A few things to understand ASAP about Redis replication.
#
#   +------------------+      +---------------+
#   |      Master      | ---> |    Replica    |
#   | (receive writes) |      |  (exact copy) |
#   +------------------+      +---------------+
#
# 1) Redis replication is asynchronous, but you can configure a master to
#    stop accepting writes if it appears to be not connected with at least
#    a given number of replicas.
# 2) Redis replicas are able to perform a partial resynchronization with the
#    master if the replication link is lost for a relatively small amount of
#    time. You may want to configure the replication backlog size (see the next
#    sections of this file) with a sensible value depending on your needs.
# 3) Replication is automatic and does not need user intervention. After a
#    network partition replicas automatically try to reconnect to masters
#    and resynchronize with them.
# 1) Redis的复制是异步的，但是你可以配置master停止写入，如果看起来没有连接时
# 2) Redis副本(从)可以执行部分重新同步，如果复制链接丢失的较少。
#    你可以配置复制积压大小(backlog)
# 3) 复制是自动的，不需要用户执行。Redis副本(从)会自动尝试重新连接master(主)，去重新同步复制。
#
# 配置语法: replicaof <master ip> <master port>
# replicaof <masterip> <masterport>

# If the master is password protected (using the "requirepass" configuration
# directive below) it is possible to tell the replica to authenticate before
# starting the replication synchronization process, otherwise the master will
# refuse the replica request.
# 如果master(主)配置了密码(使用"requirepass"进行配置)，Redis副本(从)去连接时是会被拒绝的，
# 此时需要配置master认证，配置语法: masterauth <master 密码>
# masterauth <master-password>
#
# However this is not enough if you are using Redis ACLs (for Redis version
# 6 or greater), and the default user is not capable of running the PSYNC
# command and/or other commands needed for replication. In this case it's
# better to configure a special user to use with replication, and specify the
# masteruser configuration as such:
# 如果使用了ACLs(Redis 版本6或更高版本)，上述配置是不够的，并且默认用户是无法运行PSYNC命令 和/或 其他复制所需的命令
# 此时最好配置master用户名，配置语法: masteruser <master用户名>
# masteruser <username>
#
# When masteruser is specified, the replica will authenticate against its
# master using the new AUTH form: AUTH <username> <password>.
# 如果都指定，你可以使用这样的形式配置: AUTH <username> <password>.

# When a replica loses its connection with the master, or when the replication
# is still in progress, the replica can act in two different ways:
# 当slave(从)与master断开连接时，slave是否继续，这里有两种不同的方式:
#
# 1) if replica-serve-stale-data is set to 'yes' (the default) the replica will
#    still reply to client requests, possibly with out of date data, or the
#    data set may just be empty if this is the first synchronization.
#
# 2) if replica-serve-stale-data is set to 'no' the replica will reply with
#    an error "SYNC with master in progress" to all the kind of commands
#    but to INFO, replicaOF, AUTH, PING, SHUTDOWN, REPLCONF, ROLE, CONFIG,
#    SUBSCRIBE, UNSUBSCRIBE, PSUBSCRIBE, PUNSUBSCRIBE, PUBLISH, PUBSUB,
#    COMMAND, POST, HOST: and LATENCY.
# 1) 默认是'yes'，slave(从)仍然返回给客户端，但是可能包含过期的数据，例如第一次同步时，数据可能是空的
# 2) 'no'，此时slave(从)将返回错误信息"SYNC with master in progress"
#
replica-serve-stale-data yes

# You can configure a replica instance to accept writes or not. Writing against
# a replica instance may be useful to store some ephemeral data (because data
# written on a replica will be easily deleted after resync with the master) but
# may also cause problems if clients are writing to it because of a
# misconfiguration.
# 你可以配置一个slave(从)是否可以写入。通过写入存储一个短暂的数据对于slave可能是有用的。
# 因为比起master重新同步数据，数据写入到slave更容易被删除。
# 但是如果客户端因为一个错误的配置写入，可能会造成一些问题
#
# Since Redis 2.6 by default replicas are read-only.
#
# Note: read only replicas are not designed to be exposed to untrusted clients
# on the internet. It's just a protection layer against misuse of the instance.
# Still a read only replica exports by default all the administrative commands
# such as CONFIG, DEBUG, and so forth. To a limited extent you can improve
# security of read only replicas using 'rename-command' to shadow all the
# administrative / dangerous commands.
# read only replicas设计只是用于保护slave实例，防止一些滥用
replica-read-only yes

# Replication SYNC strategy: disk or socket.
# 复制同步策略: disk(Disk-backed) 或 socket(Diskless)
#
# New replicas and reconnecting replicas that are not able to continue the
# replication process just receiving differences, need to do what is called a
# "full synchronization". An RDB file is transmitted from the master to the
# replicas.
# 新slave连接 或 老slave 重新连接 时候不能只接收不同，得做一个全同步 "full synchronization"。
# 需要一个新的RDB文件dump出来，然后从master传到slave。
#
# The transmission can happen in two different ways:
# 文件传输有两种不同的方式: 
#
# 1) Disk-backed: The Redis master creates a new process that writes the RDB
#                 file on disk. Later the file is transferred by the parent
#                 process to the replicas incrementally.
# 2) Diskless: The Redis master creates a new process that directly writes the
#              RDB file to replica sockets, without touching the disk at all.
# 1) Disk-backed: master创建一个新的进程去将RDB文件写入磁盘。之后文件由父进程增量传输到slave
# 2) Diskless: master创建一个新的进程，直接将RDB文件通过sockets传输给slave
#
# 译者注: 从这里可以看出，RDB的原理其实就是Fork和COW(Copy on Write)
#
# With disk-backed replication, while the RDB file is generated, more replicas
# can be queued and served with the RDB file as soon as the current child
# producing the RDB file finishes its work. With diskless replication instead
# once the transfer starts, new replicas arriving will be queued and a new
# transfer will start when the current one terminates.
# 当使用disk-backed复制时，一旦创建RDB文件完毕，就可以快速服务更多的slave。而如果使用 diskless复制，
# 新来的slave的排队(超过repl-diskless-sync-delay等待的秒数)，结束了才能下一个
#
# When diskless replication is used, the master waits a configurable amount of
# time (in seconds) before starting the transfer in the hope that multiple
# replicas will arrive and the transfer can be parallelized.
# 当用diskless的时候，master等待一个repl-diskless-sync-delay的秒数，
# 如果没slave来的话，就直接传，后来的得排队等了。否则并行传输
#
# With slow disks and fast (large bandwidth) networks, diskless replication
# works better.
# disk较慢，并且网络较快的时候，可以用diskless
repl-diskless-sync no

# When diskless replication is enabled, it is possible to configure the delay
# the server waits in order to spawn the child that transfers the RDB via socket
# to the replicas.
# 当使用diskless复制时，尽可能配置延迟等待时候
#
# This is important since once the transfer starts, it is not possible to serve
# new replicas arriving, that will be queued for the next RDB transfer, so the
# server waits a delay in order to let more replicas arrive.
# 这个配置是很重要的，因为一旦开始传输，后面来的slave就要进入队列排队等候。所以尽可能配置延迟等待时间，
# 让更多的slave一起并行传输
#
# The delay is specified in seconds, and by default is 5 seconds. To disable
# it entirely just set it to 0 seconds and the transfer will start ASAP.
# 上面提到的，使用diskless等待slave的时间，默认是5s，设置为0表示不启用
repl-diskless-sync-delay 5

# -----------------------------------------------------------------------------
# WARNING: RDB diskless load is experimental. Since in this setup the replica
# does not immediately store an RDB on disk, it may cause data loss during
# failovers. RDB diskless load + Redis modules not handling I/O reads may also
# cause Redis to abort in case of I/O errors during the initial synchronization
# stage with the master. Use only if your do what you are doing.
# 警告: diskless目前还是实验性阶段，如果没有立即存储在磁盘上，可能会造成数据丢失。
# 我们也可以从上面看到，默认是Disk-backed，而repl-diskless-sync no
# -----------------------------------------------------------------------------
#
# Replica can load the RDB it reads from the replication link directly from the
# socket, or store the RDB to a file and read that file after it was completely
# recived from the master.
# slave可以直接从socket中读取RDB文件，在从master那里接受后直接存储或读取RDB文件
#
# In many cases the disk is slower than the network, and storing and loading
# the RDB file may increase replication time (and even increase the master's
# Copy on Write memory and salve buffers).
# However, parsing the RDB file directly from the socket may mean that we have
# to flush the contents of the current database before the full rdb was
# received. For this reason we have the following options:
# 在很多情况下，磁盘存储比网络存储慢。存储和加载RDB文件将会增加复制时间(甚至会造成主从延迟)
# 然而，通过网络socket直接传输RDB文件，意味着我们在没有完全接受时，要刷新当前的内容。
#
# "disabled"    - Don't use diskless load (store the rdb file to the disk first)
# "on-empty-db" - Use diskless load only when it is completely safe.
# "swapdb"      - Keep a copy of the current db contents in RAM while parsing
#                 the data directly from the socket. note that this requires
#                 sufficient memory, if you don't have it, you risk an OOM kill.
# "disabled"    - 不使用diskless(优先使用disk)
# "on-empty-db" - 当完全安全的时候，仅使用diskless
# "swapdb"      - 在内存中直接复制一份DB数据，通过socket传输，但是要求内存足够，否则内存不足会被杀掉
repl-diskless-load disabled

# Replicas send PINGs to server in a predefined interval. It's possible to
# change this interval with the repl_ping_replica_period option. The default
# value is 10 seconds.
# slave发送ping到master，默认是10s
# repl-ping-replica-period 10


# The following option sets the replication timeout for:
# 在以下情况复制会超时:
#
# 1) Bulk transfer I/O during SYNC, from the point of view of replica.
# 2) Master timeout from the point of view of replicas (data, pings).
# 3) Replica timeout from the point of view of masters (REPLCONF ACK pings).
# 1) slave:大量的I/O传输 大于同步时间
# 2) master: 数据、pings超时
# 3) slave: 复制超时、ACK确认超时、pings超时
#
# It is important to make sure that this value is greater than the value
# specified for repl-ping-replica-period otherwise a timeout will be detected
# every time there is low traffic between the master and the replica.
# 复制超时时间，必须 > repl-ping-replica-period。因为这包括是从slave到master，master到slave的时间
# repl-timeout 60

# Disable TCP_NODELAY on the replica socket after SYNC?
# SYNC完毕后，在slave的socket里关闭TCP_NODELAY
#
# If you select "yes" Redis will use a smaller number of TCP packets and
# less bandwidth to send data to replicas. But this can add a delay for
# the data to appear on the replica side, up to 40 milliseconds with
# Linux kernels using a default configuration.
# 如果是 "yes"，reids会发送少量的TCP包给slave，但可能导致最高40ms的数据延迟
#
# If you select "no" the delay for data to appear on the replica side will
# be reduced but more bandwidth will be used for replication.
# 如果是no，那可能在复制的时候，会消耗 少量带宽
#
# By default we optimize for low latency, but in very high traffic conditions
# or when the master and replicas are many hops away, turning this to "yes" may
# be a good idea.
# 默认我们是为了低延迟优化而设置成no，如果主从之间有很多网络跳跃。那设置成yes吧。
repl-disable-tcp-nodelay no

# Set the replication backlog size. The backlog is a buffer that accumulates
# replica data when replicas are disconnected for some time, so that when a
# replica wants to reconnect again, often a full resync is not needed, but a
# partial resync is enough, just passing the portion of data the replica
# missed while disconnected.
# 设置复制backlog大小，backlog其实是一个缓存，当slave丢失连接的一段时间里，要存储的数据会存储到这里。
# 这样当slave重新连接后，不需要再全部重新同步，只需要将这段丢失连接里的数据，部分重新同步就好了
#
# The bigger the replication backlog, the longer the time the replica can be
# disconnected and later be able to perform a partial resynchronization.
# 这个值越大，slave可丢失连接的时间就越长
#
# The backlog is only allocated once there is at least a replica connected.
# 至少有一个slave连接时，才会分配backlog
# repl-backlog-size 1mb

# After a master has no longer connected replicas for some time, the backlog
# will be freed. The following option configures the amount of seconds that
# need to elapse, starting from the time the last replica disconnected, for
# the backlog buffer to be freed.
# 当master在一定时间没有slave连接后，backlog缓存将会被清除
#
# Note that replicas never free the backlog for timeout, since they may be
# promoted to masters later, and should be able to correctly "partially
# resynchronize" with the replicas: hence they should always accumulate backlog.
# slave是不会释放backlog，因为他们可能会变成master。为了使能够保证部分重新同步，slave要一直存储着backlog
#
# A value of 0 means to never release the backlog.
# 如果值0则永远不会释放backlog
# repl-backlog-ttl 3600

# The replica priority is an integer number published by Redis in the INFO
# output. It is used by Redis Sentinel in order to select a replica to promote
# into a master if the master is no longer working correctly.
# Redis复制的优先级是一个数字，这通常用于哨兵模式，当master挂掉时能够选取一个master
#
# A replica with a low priority number is considered better for promotion, so
# for instance if there are three replicas with priority 10, 100, 25 Sentinel
# will pick the one with priority 10, that is the lowest.
# 哨兵模式中，越小的值越容易被选中为master。例如: 10, 100, 25, 将会选中10因为它最小
#
# However a special priority of 0 marks the replica as not able to perform the
# role of master, so a replica with priority of 0 will never be selected by
# Redis Sentinel for promotion.
# 当master不能正常工作时，Redis 哨兵会从slaves中选出一个新的master，值越小越容易被选中
# 但是为0时，表示永远不会被选中
#
# By default the priority is 100.
# 默认优先级 100
replica-priority 100

# It is possible for a master to stop accepting writes if there are less than
# N replicas connected, having a lag less or equal than M seconds.
# 当slave连接数小于N，并且网络lag <= M秒时，停止master写
#
# The N replicas need to be in "online" state.
# N必须是在线状态的slave
#
# The lag in seconds, that must be <= the specified value, is calculated from
# the last ping received from the replica, that is usually sent every second.
#
# This option does not GUARANTEE that N replicas will accept the write, but
# will limit the window of exposure for lost writes in case not enough replicas
# are available, to the specified number of seconds.
#
# For example to require at least 3 replicas with a lag <= 10 seconds use:
# 例如: N: 3 M:10，
#
# min-replicas-to-write 3
# min-replicas-max-lag 10
#
# Setting one or the other to 0 disables the feature.
#
# By default min-replicas-to-write is set to 0 (feature disabled) and
# 默认min-replicas-to-write 0，min-replicas-max-lag 10
# 表示slave无限制
# min-replicas-max-lag is set to 10.

# A Redis master is able to list the address and port of the attached
# replicas in different ways. For example the "INFO replication" section
# offers this information, which is used, among other tools, by
# Redis Sentinel in order to discover replica instances.
# Another place where this info is available is in the output of the
# "ROLE" command of a master.
# Redis的master可以列出slave地址和端口之类的信息，有多种方式。例如：
# 1. "INFO replication"命令
# 3. "ROLE"命令
#
# The listed IP and address normally reported by a replica is obtained
# in the following way:
# 列出IP 和 address通常有以下几种方式:
#
#   IP: The address is auto detected by checking the peer address
#   of the socket used by the replica to connect with the master.
#
#   Port: The port is communicated by the replica during the replication
#   handshake, and is normally the port that the replica is using to
#   listen for connections.
#
# However when port forwarding or Network Address Translation (NAT) is
# used, the replica may be actually reachable via different IP and port
# pairs. The following two options can be used by a replica in order to
# report to its master a specific set of IP and port, so that both INFO
# and ROLE will report those values.
#
# There is no need to use both the options if you need to override just
# the port or the IP address.
#
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234
```

### KEYS TRACKING  KEYS跟踪

```text
############################### KEYS TRACKING #################################
# KEYS跟踪
# 
# Redis implements server assisted support for client side caching of values.
# This is implemented using an invalidation table that remembers, using
# 16 millions of slots, what clients may have certain subsets of keys. In turn
# this is used in order to send invalidation messages to clients. Please
# to understand more about the feature check this page:
# Redis实现了客户端对值的缓存。这个缓存实现是通过一张无效表(invalidation table)记录的，
# 使用1600万插槽，哪些客户端可以拥有哪些密钥子集。反过来，可以向客户端发送invalidation信息。
# 更多详细请阅读下面链接:
#
#   https://redis.io/topics/client-side-caching
#
# When tracking is enabled for a client, all the read only queries are assumed
# to be cached: this will force Redis to store information in the invalidation
# table. When keys are modified, such information is flushed away, and
# invalidation messages are sent to the clients. However if the workload is
# heavily dominated by reads, Redis could use more and more memory in order
# to track the keys fetched by many clients.
#
# For this reason it is possible to configure a maximum fill value for the
# invalidation table. By default it is set to 1M of keys, and once this limit
# is reached, Redis will start to evict keys in the invalidation table
# even if they were not modified, just to reclaim memory: this will in turn
# force the clients to invalidate the cached values. Basically the table
# maximum size is a trade off between the memory you want to spend server
# side to track information about who cached what, and the ability of clients
# to retain cached objects in memory.
#
# If you set the value to 0, it means there are no limits, and Redis will
# retain as many keys as needed in the invalidation table.
# In the "stats" INFO section, you can find information about the number of
# keys in the invalidation table at every given moment.
#
# Note: when key tracking is used in broadcasting mode, no memory is used
# in the server side so this setting is useless.
#
# tracking-table-max-keys 1000000
```

### SECURITY 安全

```text
################################## SECURITY ###################################
# 安全
#
# Warning: since Redis is pretty fast an outside user can try up to
# 1 million passwords per second against a modern box. This means that you
# should use very strong passwords, otherwise they will be very easy to break.
# Note that because the password is really a shared secret between the client
# and the server, and should not be memorized by any human, the password
# can be easily a long string from /dev/urandom or whatever, so by using a
# long and unguessable password no brute force attack will be possible.
# 警告: Redis被外部访问是非常快的，可以达到每秒100万。因此你应该用一个强密码，否则很容易被破解
# 另外密码是保证Redis服务器和客户端连接的，不要随便告诉别人。

# Redis ACL users are defined in the following format:
# Redis ACL(访问控制列表)定义格式如下:
#   user <username> ... acl rules ...
#
# For example:
# 例如: 
#   user worker +@list +@connection ~jobs:* on >ffa9203c493aa99
#
# The special username "default" is used for new connections. If this user
# has the "nopass" rule, then new connections will be immediately authenticated
# as the "default" user without the need of any password provided via the
# AUTH command. Otherwise if the "default" user is not flagged with "nopass"
# the connections will start in not authenticated state, and will require
# AUTH (or the HELLO command AUTH option) in order to be authenticated and
# start to work.
# 如果标记为"nopass" 的用户，不需要密码认证，否则需要通过AUTH命令进行认证
#
# The ACL rules that describe what an user can do are the following:
# ACL规则说明:
#
#  on           Enable the user: it is possible to authenticate as this user.
#               
#  off          Disable the user: it's no longer possible to authenticate
#               with this user, however the already authenticated connections
#               will still work.
#  +<command>   Allow the execution of that command
#               允许执行的命令
#  -<command>   Disallow the execution of that command
#               不允许执行的命令
#  +@<category> Allow the execution of all the commands in such category
#               with valid categories are like @admin, @set, @sortedset, ...
#               and so forth, see the full list in the server.c file where
#               the Redis command table is described and defined.
#               The special category @all means all the commands, but currently
#               present in the server, and that will be loaded in the future
#               via modules.
#               允许执行分类下的所有命令，分类例如:@admin, @set, @sortedset, ...，想了解全部列表，
#               可以查看server.c 文件下的描述。@all表示所有
#  +<command>|subcommand    Allow a specific subcommand of an otherwise
#                           disabled command. Note that this form is not
#                           allowed as negative like -DEBUG|SEGFAULT, but
#                           only additive starting with "+".
#  allcommands  Alias for +@all. Note that it implies the ability to execute
#               all the future commands loaded via the modules system.
#               +@all的别名
#  nocommands   Alias for -@all.
#               -@all的别名
#  ~<pattern>   Add a pattern of keys that can be mentioned as part of
#               commands. For instance ~* allows all the keys. The pattern
#               is a glob-style pattern like the one of KEYS.
#               It is possible to specify multiple patterns.
#               支持正则表达式
#  allkeys      Alias for ~*
#                ~* 别名
#  resetkeys    Flush the list of allowed keys patterns.
#  ><password>  Add this passowrd to the list of valid password for the user.
#               For example >mypass will add "mypass" to the list.
#               This directive clears the "nopass" flag (see later).
#  <<password>  Remove this password from the list of valid passwords.
#  nopass       All the set passwords of the user are removed, and the user
#               is flagged as requiring no password: it means that every
#               password will work against this user. If this directive is
#               used for the default user, every new connection will be
#               immediately authenticated with the default user without
#               any explicit AUTH command required. Note that the "resetpass"
#               directive will clear this condition.
#  resetpass    Flush the list of allowed passwords. Moreover removes the
#               "nopass" status. After "resetpass" the user has no associated
#               passwords and there is no way to authenticate without adding
#               some password (or setting it as "nopass" later).
#  reset        Performs the following actions: resetpass, resetkeys, off,
#               -@all. The user returns to the same state it has immediately
#               after its creation.
#
# ACL rules can be specified in any order: for instance you can start with
# passwords, then flags, or key patterns. However note that the additive
# and subtractive rules will CHANGE MEANING depending on the ordering.
# For instance see the following example:
#
#   user alice on +@all -DEBUG ~* >somepassword
#
# This will allow "alice" to use all the commands with the exception of the
# DEBUG command, since +@all added all the commands to the set of the commands
# alice can use, and later DEBUG was removed. However if we invert the order
# of two ACL rules the result will be different:
#
#   user alice on -DEBUG +@all ~* >somepassword
#
# Now DEBUG was removed when alice had yet no commands in the set of allowed
# commands, later all the commands are added, so the user will be able to
# execute everything.
#
# Basically ACL rules are processed left-to-right.
#
# For more information about ACL configuration please refer to
# the Redis web site at https://redis.io/topics/acl

# ACL LOG
#
# The ACL Log tracks failed commands and authentication events associated
# with ACLs. The ACL Log is useful to troubleshoot failed commands blocked 
# by ACLs. The ACL Log is stored in and consumes memory. There is no limit
# to its length.You can reclaim memory with ACL LOG RESET or set a maximum
# length below.
# ACL日志跟踪，当有用户认证失败时就会记录。ACL日志最大长度限制
acllog-max-len 128

# Using an external ACL file
# 使用外部ACL文件
#
# Instead of configuring users here in this file, it is possible to use
# a stand-alone file just listing users. The two methods cannot be mixed:
# if you configure users here and at the same time you activate the exteranl
# ACL file, the server will refuse to start.
# 与其在此文件中配置用户，还可以使用仅列出用户的独立文件。这两种方法不能混用：
# 如果在此处配置用户，同时激活外部ACL文件，服务器将拒绝启动。
#
# The format of the external ACL user file is exactly the same as the
# format that is used inside redis.conf to describe users.
# 配置格式:
# aclfile /etc/redis/users.acl

# IMPORTANT NOTE: starting with Redis 6 "requirepass" is just a compatiblity
# layer on top of the new ACL system. The option effect will be just setting
# the password for the default user. Clients will still authenticate using
# AUTH <password> as usually, or more explicitly with AUTH default <password>
# if they follow the new protocol: both will work.
# 重点: Redis 6开始"requirepass" 只是一个兼容新ACL系统的顶层。这个只是用于设置默认用户的密码
# 客户端仍然需使用AUTH <password> 命令格式认证
#
# requirepass foobared

# Command renaming (DEPRECATED).
# 命令重命名
#
# ------------------------------------------------------------------------
# WARNING: avoid using this option if possible. Instead use ACLs to remove
# commands from the default user, and put them only in some admin user you
# create for administrative purposes.
# 警告: 避免使用此选项
# ------------------------------------------------------------------------
#
# It is possible to change the name of dangerous commands in a shared
# environment. For instance the CONFIG command may be renamed into something
# hard to guess so that it will still be available for internal-use tools
# but not available for general clients.
# 在共享环境里，可以将一些危险命令重命名，例如将CONFIG命令重命名为难以猜测的
#
# Example:
# 例如:
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# It is also possible to completely kill a command by renaming it into
# an empty string:
# 将命令重命名为一个空的字符串""
#
# rename-command CONFIG ""
#
# Please note that changing the name of commands that are logged into the
# AOF file or transmitted to replicas may cause problems.
# 注意，重命名命令添加到AOF文件中，slave可能会造成一些问题
```

### CLIENTS 客户端

```text
################################### CLIENTS ####################################

# Set the max number of connected clients at the same time. By default
# this limit is set to 10000 clients, however if the Redis server is not
# able to configure the process file limit to allow for the specified limit
# the max number of allowed clients is set to the current file limit
# minus 32 (as Redis reserves a few file descriptors for internal uses).
# 设置客户端最大连接数，默认限制是10000
#
# Once the limit is reached Redis will close all the new connections sending
# an error 'max number of clients reached'.
# 因此超过限制的连接，REdis会关闭新连接，并返回'max number of clients reached'错误信息
#
# maxclients 10000
```

### MEMORY MANAGEMENT 内存管理

```text
############################## MEMORY MANAGEMENT ################################

# Set a memory usage limit to the specified amount of bytes.
# When the memory limit is reached Redis will try to remove keys
# according to the eviction policy selected (see maxmemory-policy).
# 当内存空间达到一定的限制大小时，Redis会根据内存淘汰机制(maxmemory-policy)移除一些keys
#
# If Redis can't remove keys according to the policy, or if the policy is
# set to 'noeviction', Redis will start to reply with errors to commands
# that would use more memory, like SET, LPUSH, and so on, and will continue
# to reply to read-only commands like GET.
# 如果Redis淘汰策略设置为'noeviction'，Redis所有写操作将会返回错误信息，例如SET，LPUSH等
# 但是仍然可以读操作
#
# This option is usually useful when using Redis as an LRU or LFU cache, or to
# set a hard memory limit for an instance (using the 'noeviction' policy).
# 当Redis使用LRU算法或LFU缓存，或设置'noeviction' 策略时，这个设置蛮有用的
#
# WARNING: If you have replicas attached to an instance with maxmemory on,
# the size of the output buffers needed to feed the replicas are subtracted
# from the used memory count, so that network problems / resyncs will
# not trigger a loop where keys are evicted, and in turn the output
# buffer of replicas is full with DELs of keys evicted triggering the deletion
# of more keys, and so forth until the database is completely emptied.
# 警告: 如果slave设置了maxmemory，需要减去slave output buffers。为了网络传输或重新同步时，
# 不触发循环回收key，并反过来触发输出到outpu buffer，删除的key将被删除，
# 直到数据库是完全清空
#
# In short... if you have replicas attached it is suggested that you set a lower
# limit for maxmemory so that there is some free RAM on the system for replica
# output buffers (but this is not needed if the policy is 'noeviction').
# 简单来说，如果你有slave，你应该把最大内存别设置的太大，
# 留一些系统内存给slave output buffers(如果是noeviction策略，就无需这样设置了)
# maxmemory <bytes>

# MAXMEMORY POLICY: how Redis will select what to remove when maxmemory
# is reached. You can select one from the following behaviors:
# 内存淘汰策略机制:
#
# volatile-lru -> Evict using approximated LRU, only keys with an expire set.
# allkeys-lru -> Evict any key using approximated LRU.
# volatile-lfu -> Evict using approximated LFU, only keys with an expire set.
# allkeys-lfu -> Evict any key using approximated LFU.
# volatile-random -> Remove a random key having an expire set.
# allkeys-random -> Remove a random key, any key.
# volatile-ttl -> Remove the key with the nearest expire time (minor TTL)
# noeviction -> Don't evict anything, just return an error on write operations.

# Evict: 移除的意思
# volatile-lru -> 使用LRU算法移除key，只对设置了过期时间的键
# allkeys-lru -> 使用LRU移除key
# volatile-lfu -> 使用LFU算法移除key，只对设置了过期时间的键
# allkeys-lfu -> 使用LFU移除key
# volatile-random -> 在过期集合中移除随机的key，只对设置了过期时间的key
# allkeys-random -> 在过期集合中移除随机的key
# volatile-ttl -> 移除TTL值最小的key，即最近要过期的key
# noeviction -> 不移除，返回错误

#
# LRU means Least Recently Used
# LFU means Least Frequently Used
# LRU (Least Recently Used)表示最近最少使用
# LFU (Least Frequently Used)表示最不常用
#
# Both LRU, LFU and volatile-ttl are implemented using approximated
# randomized algorithms.
# LRU, LFU and volatile-ttl 使用的是近似随机算法
#
# Note: with any of the above policies, Redis will return an error on write
#       operations, when there are no suitable keys for eviction.
# 记住: 当使用上面说的那些策略，如果没有合适的key可以溢出，Redis在写操作时会返回错误
#       
#       
#       At the date of writing these commands are: 
#       写命令
#       set setnx setex append
#       incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd
#       sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby
#       zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby
#       getset mset msetnx exec sort
#
# The default is:
# 默认策略
# maxmemory-policy noeviction

# LRU, LFU and minimal TTL algorithms are not precise algorithms but approximated
# algorithms (in order to save memory), so you can tune it for speed or
# accuracy. For default Redis will check five keys and pick the one that was
# used less recently, you can change the sample size using the following
# configuration directive.
# LRU、LFU和最小TTL算法不是精确算法，而是近似算法算法（为了节省内存），
# 因此您可以调整它的速度或准确度。默认情况下，Redis将检查五个键，
#并选择一个最近使用较少，可以使用以下命令更改样本大小配置指令。
#
# The default of 5 produces good enough results. 10 Approximates very closely
# true LRU but costs more CPU. 3 is faster but not very accurate.
# 默认值是5，能产生足够好的结果。十分接近真正的LRU，但需要更多的CPU。
# 3更快，但不太准确。
#
# maxmemory-samples 5

# Starting from Redis 5, by default a replica will ignore its maxmemory setting
# (unless it is promoted to master after a failover or manually). It means
# that the eviction of keys will be just handled by the master, sending the
# DEL commands to the replica as keys evict in the master side.
# 从Redis 5开始，slave默认是忽略maxmemory设置的(除非在故障转移或手动设置为master，才不会被忽略)
# 意思就是说移除keys取决于master。
#
# This behavior ensures that masters and replicas stay consistent, and is usually
# what you want, however if your replica is writable, or you want the replica
# to have a different memory setting, and you are sure all the writes performed
# to the replica are idempotent, then you may change this default (but be sure
# to understand what you are doing).
# 这样的设定确保了master和slave保持一致，这通常是你想要的。然而如果slave是可写的，
# 或者你想设置一个不同内存，并且你确定slave写是幂等(idempotent)的，那你就可以改变这默认的设置(但是你要知道你在做什么)
#
# Note that since the replica by default does not evict, it may end using more
# memory than the one set via maxmemory (there are certain buffers that may
# be larger on the replica, or data structures may sometimes take more memory
# and so forth). So make sure you monitor your replicas and make sure they
# have enough memory to never hit a real out-of-memory condition before the
# master hits the configured maxmemory setting.
# 注意，由于默认情况下slave不会移除，因此它可能会使用更多内存大于通过maxmemory设置的内存
# (有些缓冲区可能在slave更大，或者数据结构有时可能占用更多内存等等)。
# 所以你一定要监控好slave，确保在使用master的maxmemory设置时，slave有足够的内存(不要内存溢出)
#
# replica-ignore-maxmemory yes

# Redis reclaims expired keys in two ways: upon access when those keys are
# found to be expired, and also in background, in what is called the
# "active expire key". The key space is slowly and interactively scanned
# looking for expired keys to reclaim, so that it is possible to free memory
# of keys that are expired and will never be accessed again in a short time.
# Redis以两种方式回收过期的keys: 当这些keys发现是已过期，也在后台，被称为"激活过期的key”。
# key空间缓慢地交互扫描寻找回收要过期的key，以便释放 已过期且在短时间内无法再次访问的key占用的内存
#
# The default effort of the expire cycle will try to avoid having more than
# ten percent of expired keys still in memory, and will try to avoid consuming
# more than 25% of total memory and to add latency to the system. However
# it is possible to increase the expire "effort" that is normally set to
# "1", to a greater value, up to the value "10". At its maximum value the
# system will use more CPU, longer cycles (and technically may introduce
# more latency), and will tollerate less already expired keys still present
# in the system. It's a tradeoff betweeen memory, CPU and latecy.
# 过期周期性的工作，以尝试避免10%的过期key仍在内存中，并将尽量避免使用超过总内存的25%并向系统添加延迟。
# 然而 可以增加过期"effort"，设置为"1"表示更大的值。最大值为"10"。
# 在最大值时，系统将使用更多的CPU，更长的周期（技术上可能会引入更大的延迟），并且将容忍更少的已经过期的key仍然存在
# 在系统中，这是内存、CPU和延迟之间的权衡。
#
# active-expire-effort 1
```

### LAZY FREEING 懒释放\(阻塞和非阻塞\)

```text
############################# LAZY FREEING ####################################
# 懒释放(阻塞和非阻塞)

# Redis has two primitives to delete keys. One is called DEL and is a blocking
# deletion of the object. It means that the server stops processing new commands
# in order to reclaim all the memory associated with an object in a synchronous
# way. If the key deleted is associated with a small object, the time needed
# in order to execute the DEL command is very small and comparable to most other
# O(1) or O(log_N) commands in Redis. However if the key is associated with an
# aggregated value containing millions of elements, the server can block for
# a long time (even seconds) in order to complete the operation.
# Redis有两种原始方式用于删除key。一个叫做DEL，是一个阻塞删除对象。
# 这意味着服务器停止处理新命令，为了在同步中回收与对象关联的所有内存。
# 如果删除的key与一个小对象关联，与大多数其他在 O(1) or O(log_N)命令相比，则执行DEL命令所需的时间非常小，
# 但是，如果key与包含数百万个元素的聚合，服务器需要花费长时间（甚至几秒钟）来完成操作。
#
# For the above reasons Redis also offers non blocking deletion primitives
# such as UNLINK (non blocking DEL) and the ASYNC option of FLUSHALL and
# FLUSHDB commands, in order to reclaim memory in background. Those commands
# are executed in constant time. Another thread will incrementally free the
# object in the background as fast as possible.
# 出于上述原因，Redis还提供了 非阻塞删除 原始key。
# 例如: UNLINK(non-blocking DEL) 和 FLUSHALL的ASYNC选项、FLUSHDB命令。
# 以便在后台回收内存。那些命令在固定时间内执行。另一个线程将在后台里尽快的逐渐释放
#
#
# DEL, UNLINK and ASYNC option of FLUSHALL and FLUSHDB are user-controlled.
# It's up to the design of the application to understand when it is a good
# idea to use one or the other. However the Redis server sometimes has to
# delete keys or flush the whole database as a side effect of other operations.
# Specifically Redis deletes objects independently of a user call in the
# following scenarios:
# DEL, UNLINK and ASYNC option of FLUSHALL and FLUSHDB是用户控制的。你可以自己选择合适的实际应用。
# 但是，有时候，Redis不得不主动删除keys或者flush整个数据库以便其他操作时，这样有以下几种情况会造成这个发生：

# 1) On eviction, because of the maxmemory and maxmemory policy configurations,
#    in order to make room for new data, without going over the specified
#    memory limit.
# 2) Because of expire: when a key with an associated time to live (see the
#    EXPIRE command) must be deleted from memory.
# 3) Because of a side effect of a command that stores data on a key that may
#    already exist. For example the RENAME command may delete the old key
#    content when it is replaced with another one. Similarly SUNIONSTORE
#    or SORT with STORE option may delete existing keys. The SET command
#    itself removes any old content of the specified key in order to replace
#    it with the specified string.
# 4) During replication, when a replica performs a full resynchronization with
#    its master, the content of the whole database is removed in order to
#    load the RDB file just transferred.
# 1) 内存移除策略，maxmemory policy
# 2) 因为过期了，所以从内存种移除
# 3) 存储了一些已存在的数据，例如RENAME命令，将会删除旧的key。类似的，SUNIONSTORE和SORT也会删除存在的keys。SET也会
# 4) 当slave需要全同步的时，Redis会清除整个数据库，为了能将RDB文件传输过来。
#
# In all the above cases the default is to delete objects in a blocking way,
# like if DEL was called. However you can configure each case specifically
# in order to instead release memory in a non-blocking way like if UNLINK
# was called, using the following configuration directives.
# 在上述提到的情况，删除的时候将会以阻塞的方式进行。例如DEL。然而你可以根据每一个情况，配置非阻塞的方式 释放key。
# 译者注: lazy意味着非阻塞

lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no

# It is also possible, for the case when to replace the user code DEL calls
# with UNLINK calls is not easy, to modify the default behavior of the DEL
# command to act exactly like UNLINK, using the following configuration
# directive:

lazyfree-lazy-user-del no
```

### THREADED I/O 线程I/O

```text
################################ THREADED I/O #################################
# 线程I/O
# Redis is mostly single threaded, however there are certain threaded
# operations such as UNLINK, slow I/O accesses and other things that are
# performed on side threads.
# Redis主要是单线程的，然而有些操作，例如UNLINK、 slow I/O处理或其他的时候，需要开始额外的线程
#
# Now it is also possible to handle Redis clients socket reads and writes
# in different I/O threads. Since especially writing is so slow, normally
# Redis users use pipelining in order to speedup the Redis performances per
# core, and spawn multiple instances in order to scale more. Using I/O
# threads it is possible to easily speedup two times Redis without resorting
# to pipelining nor sharding of the instance.
# Redis可以在不同的I/O线程中处理客户端socket的读写。
# 因为写是特别慢的，通常Redis用户可以使用管道(pipe)来加速Redis的性能核心，
# 并生成多个实例以扩大规模。还可以使用I/O线程轻松地加速Redis两次，而无需重新调用
# 管道运输(pipelining)或分片。
#
# By default threading is disabled, we suggest enabling it only in machines
# that have at least 4 or more cores, leaving at least one spare core.
# Using more than 8 threads is unlikely to help much. We also recommend using
# threaded I/O only if you actually have performance problems, with Redis
# instances being able to use a quite big percentage of CPU time, otherwise
# there is no point in using this feature.
# 默认情况是禁用线程的，我们建议你的机器在至少4核时才开启，至少一个备用核心。
# 使用8个以上不会有太多帮助，我们建议只有当你确实有性能问题时才使用线程 I/O，
# 使得Redis能够使用相当大比例的CPU时间，否则使用此功能没有意义。
#
# So for instance if you have a four cores boxes, try to use 2 or 3 I/O
# threads, if you have a 8 cores, try to use 6 threads. In order to
# enable I/O threads use the following configuration directive:
# 如果你是4核，试一下用2 或 3个 线程I/O。
# 如果是8核，试一下用6个。
# 你可以通过下面去配置:

# io-threads 4
#
# Setting io-threads to 1 will just use the main thread as usually.
# When I/O threads are enabled, we only use threads for writes, that is
# to thread the write(2) syscall and transfer the client buffers to the
# socket. However it is also possible to enable threading of reads and
# protocol parsing using the following configuration directive, by setting
# it to yes:
# 将I/O线程设置为 1时，将只使用主线程。
# 当I/O线程被启用时，我们只使用线程进行写操作，即执行写入(2)系统调用并将客户端缓冲区传输到socket。
# 但是，也可以使用下面的配置命令，进行启用读取和协议解析。
#
# io-threads-do-reads no
#
# Usually threading reads doesn't help much.
# 通常来说线程读不会帮助太多
#
# NOTE 1: This configuration directive cannot be changed at runtime via
# CONFIG SET. Aso this feature currently does not work when SSL is
# enabled.
# 注意1: 在Redis运行时，这个配置不能通过CONFIG SET设置，当SSL开启时。
#
# NOTE 2: If you want to test the Redis speedup using redis-benchmark, make
# sure you also run the benchmark itself in threaded mode, using the
# --threads option to match the number of Redis theads, otherwise you'll not
# be able to notice the improvements.
# 注意2: 如果你想测试Redis的性能，可以用redis-benchmark。
# 另外你可以使用--threads参数设置Redis线程，不然你无法注意到性能的提高
```

### APPEND ONLY MODE 追加模式\(APPEND ONLY FILE俗称的AOF\)

```text
############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
# 默认Redis异步的dump数据到磁盘，这种情况很多时候已经够用了。但是有一个问题是，
# 当Redis断电时，那么就会丢失一段时间写的数据(取决于保存点 save points)
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
# AOF是另一种持久化方式，持久化更好。例如使用数据fsync策略(看后面配置)时，如果出现断电故障情况，
# Redis只丢失最近一秒的数据，或者最后一个write操作(redis自身错误，os正常)。每个write操作写一次AOF。
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
# 可以同时启动AOF和RDB是没有问题的。如果启用了AOF，Redis启动的时候会优先加载AOF，
# AOF的文件保存的内容更好。
# 译者注: 从这里也可以看出，当同时启用AOF和RDB时，因为AOF保存的内容更全，因此优先加载。
#
# Please check http://redis.io/topics/persistence for more information.
# 更多信息请阅读: http://redis.io/topics/persistence
# 译者注: 默认不开启AOF，因为大部分情况下RDB完全够用
appendonly no

# The name of the append only file (default: "appendonly.aof")
# 默认名称："appendonly.aof"

appendfilename "appendonly.aof"

# The fsync() call tells the Operating System to actually write data on disk
# instead of waiting for more data in the output buffer. Some OS will really flush
# data on disk, some other OS will just try to do it ASAP.
# 调用fsync() 可以告诉操作系统在磁盘上实际写入数据，
# 而不是等待输出缓冲区中的更多数据。一些操作系统会真正刷新磁盘上的数据，其他一些操作系统会尽快完成。
# 译者注: ASAP(as soon as possible)
#
# Redis supports three different modes:
# Redis支持三种不同的模式:
#
# no: don't fsync, just let the OS flush the data when it wants. Faster.
# always: fsync after every write to the append only log. Slow, Safest.
# everysec: fsync only one time every second. Compromise.
# no: 不要fsync()，让系统想刷新的时候再刷新，这种最快。
# always: 当写操作时马上fsync()到append only log， 这种慢，但是最安全。
# everysec: 每秒一次fsync()，更合适、折中
#
# The default is "everysec", as that's usually the right compromise between
# speed and data safety. It's up to you to understand if you can relax this to
# "no" that will let the operating system flush the output buffer when
# it wants, for better performances (but if you can live with the idea of
# some data loss consider the default persistence mode that's snapshotting),
# or on the contrary, use "always" that's very slow but a bit safer than
# everysec.
# 默认是"everysec"，这是在速度和安全上做了折中考虑。总的来说，
# 从速度上看: no > everysec > always
# 从安全上看: no < everysec < always
#
# More details please check the following article:
# 更多细节请阅读下面文章:
# http://antirez.com/post/redis-persistence-demystified.html
#
# If unsure, use "everysec".
# 如果你不懂，就用"everysec".

# appendfsync always
appendfsync everysec
# appendfsync no

# When the AOF fsync policy is set to always or everysec, and a background
# saving process (a background save or AOF log background rewriting) is
# performing a lot of I/O against the disk, in some Linux configurations
# Redis may block too long on the fsync() call. Note that there is no fix for
# this currently, as even performing fsync in a different thread will block
# our synchronous write(2) call.
# 当fsync为always或者everysec时，如果一个BGSAVE或者AOF rewrite线程正在耗费大量I/0，Redis可能会在fsync上阻塞很久。
# 发生之后就无法fix，即使是另一个线程跑fsync，也会阻塞我们同步的write方法。
#
# In order to mitigate this problem it's possible to use the following option
# that will prevent fsync() from being called in the main process while a
# BGSAVE or BGREWRITEAOF is in progress.
# 为了解决这个问题：当 BGSAVE or BGREWRITEAOF在运行时，主进程的fsync()就无法调用。

# This means that while another child is saving, the durability of Redis is
# the same as "appendfsync none". In practical terms, this means that it is
# possible to lose up to 30 seconds of log in the worst scenario (with the
# default Linux settings).
# 意思是说，当子进程在save时，相当于"appendfsync none"。
# 实际上这意味着，最坏的情况就是有可能会丢失最多30s的log(使用Linux默认设置)
#
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.
# 所以如果你有latency问题，把下边改成yes，否则就用no。从持久性来看"no"是最安全的。
# 
# "yes"意思是暂停aof，拒绝主进程的这次fsync。
# "no"是Redis排队，不会被prevent了，但主进程是阻塞的。

no-appendfsync-on-rewrite no

# Automatic rewrite of the append only file.
# Redis is able to automatically rewrite the log file implicitly calling
# BGREWRITEAOF when the AOF log size grows by the specified percentage.
# 自动重写AOF
# 当AOF文件大小到一定比例时，就会自动隐式调用BGREWRITEAOF
#
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
# 工作流程: Redis记住最后一次rewrite时AOF文件大小(重启后没rewrite的话，就是启动时AOF文件的大小)


# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
# 对比文件的大小，如果现在AOF大小和上次大小对比，比例达到特定值就重写。
# 你需要指定最小AOF大小，有助于避免重写AOF(当比例已经达到，但是文件很小的情况)
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.
# percentage设置为0就是禁用主动重写AOF

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# An AOF file may be found to be truncated at the end during the Redis
# startup process, when the AOF data gets loaded back into memory.
# This may happen when the system where Redis is running
# crashes, especially when an ext4 filesystem is mounted without the
# data=ordered option (however this can't happen when Redis itself
# crashes or aborts but the operating system still works correctly).
# 在Redis启动期间，当AOF数据被加载回内存时，可能会发现AOF文件在末尾被截断。
# 这可能是运行Redis的系统时崩溃出现的，特别是在ext4文件系统，没有data=ordered选项时
# (但是当Redis自己造成的崩溃或中止，但操作系统仍然正常工作)。

# Redis can either exit with an error when this happens, or load as much
# data as possible (the default now) and start if the AOF file is found
# to be truncated at the end. The following option controls this behavior.
# Redis在发现AOF文件被截断时，可以退出或加载，可以通过下面的参数进行配置。
#
# If aof-load-truncated is set to yes, a truncated AOF file is loaded and
# the Redis server starts emitting a log to inform the user of the event.
# Otherwise if the option is set to no, the server aborts with an error
# and refuses to start. When the option is set to no, the user requires
# to fix the AOF file using the "redis-check-aof" utility before to restart
# the server.
# aof-load-truncated设置为 "yes"，出现截断时，Redis会发送一个消息事件通过用户。
# aof-load-truncated设置为 "no"，Redis会终止，并返回错误信息。
# 当设置为"no"时，需要"redis-check-aof"来修复AOF文件才能重新启动Redis。
#
# Note that if the AOF file will be found to be corrupted in the middle
# the server will still exit with an error. This option only applies when
# Redis will try to read more data from the AOF file but not enough bytes
# will be found.
# 注意，如果在中间发现AOF文件已损坏，Redis仍将退出并出现错误。
# 此选项仅适用于Redis将尝试从AOF文件中读取更多数据，但找到的字节数不足。
aof-load-truncated yes

# When rewriting the AOF file, Redis is able to use an RDB preamble in the
# AOF file for faster rewrites and recoveries. When this option is turned
# on the rewritten AOF file is composed of two different stanzas:
# 当重写AOF文件时，Redis能够使用前面的RDB文件，快速重写和恢复AOF文件。
# 需要配置两个参数，如下:
#
#   [RDB file][AOF tail]
#
# When loading Redis recognizes that the AOF file starts with the "REDIS"
# string and loads the prefixed RDB file, and continues loading the AOF
# tail.
# 加载Redis时，可以识别以"Redis"开头的字符串的AOF文件，并加载前缀RDB文件，然后继续加载后面的AOF。
# 译者注: preamble(序言，开头)
aof-use-rdb-preamble yes
```

### LUA SCRIPTING LUA脚本

```text
################################ LUA SCRIPTING  ###############################
# LUA脚本
#
# Max execution time of a Lua script in milliseconds.
# LUA脚本最常的执行时间，单位毫秒
#
# If the maximum execution time is reached Redis will log that a script is
# still in execution after the maximum allowed time and will start to
# reply to queries with an error.
# 如果执行LUA脚本达到了限制的最大时间，Redis会记录日志，并返回错误信息
#
# When a long running script exceeds the maximum execution time only the
# SCRIPT KILL and SHUTDOWN NOSAVE commands are available. The first can be
# used to stop a script that did not yet called write commands. The second
# is the only way to shut down the server in the case a write command was
# already issued by the script but the user doesn't want to wait for the natural
# termination of the script.
# 当一个脚本超过了最大时限。只有SCRIPT KILL和SHUTDOWN NOSAVE可以用。
# 第一个可以杀没有调write命令的东西。要是已经调用了write，只能用第二个命令杀。
#
# Set it to 0 or a negative value for unlimited execution without warnings.
# 设置为0 或者 负数， 则就是没有限制
lua-time-limit 5000
```

### REDIS CLUSTER Redis集群

```text
################################ REDIS CLUSTER  ###############################
# Redis集群
# 
# Normal Redis instances can't be part of a Redis Cluster; only nodes that are
# started as cluster nodes can. In order to start a Redis instance as a
# cluster node enable the cluster support uncommenting the following:
# 普通的Redis实例是没有集群的，只有开启了集群才是集群节点。你可以取消下面的注释开启集群
#
# cluster-enabled yes

# Every cluster node has a cluster configuration file. This file is not
# intended to be edited by hand. It is created and updated by Redis nodes.
# Every Redis Cluster node requires a different cluster configuration file.
# Make sure that instances running in the same system do not have
# overlapping cluster configuration file names.
# 每一个集群节点都有一个集群配置文件，这个文件不是手动编辑的，由Redis主动创建和更新。
# 每个集群节点要求配置是不同的，所以文件名字不要相同。
#
# cluster-config-file nodes-6379.conf

# Cluster node timeout is the amount of milliseconds a node must be unreachable
# for it to be considered in failure state.
# Most other internal time limits are multiple of the node timeout.
# 集群节点超时设置
# 当无法访问时间达到多少毫秒时，标记为不可用状态。
#
# cluster-node-timeout 15000

# A replica of a failing master will avoid to start a failover if its data
# looks too old.
# 如果master发生故障，slave将会避免故障转移，如果数据看起来太旧了
#
# There is no simple way for a replica to actually have an exact measure of
# its "data age", so the following two checks are performed:
# 对于slave来说，没有精确的方式去测量数据年龄("data age")，所以需要以下两个检测:
#
# 1) If there are multiple replicas able to failover, they exchange messages
#    in order to try to give an advantage to the replica with the best
#    replication offset (more data from the master processed).
#    Replicas will try to get their rank by offset, and apply to the start
#    of the failover a delay proportional to their rank.
# 1) 如果有多个slave进行故障转移，它们会交换信息，以至于找到最好的复制偏移量(从master处理更多的数据)
#    slave将尝试按偏移量 进行排名，并应用于开始故障转移的延迟 与 其等级成正比。
# 
# 2) Every single replica computes the time of the last interaction with
#    its master. This can be the last ping or command received (if the master
#    is still in the "connected" state), or the time that elapsed since the
#    disconnection with the master (if the replication link is currently down).
#    If the last interaction is too old, the replica will not try to failover
#    at all.
# 2) 每个slave计算一下最后一次与master的交互时间。
#    这可能是接收到的最后一个ping或命令(如果仍处于"已连接"状态)，
#    或主动与master断开连接(如果复制链接当前已关闭)。
#    如果上次交互时间太旧，则slave将不会尝试故障转移
#
# The point "2" can be tuned by user. Specifically a replica will not perform
# the failover if, since the last interaction with the master, the time
# elapsed is greater than:
# 有两点是可以自己设定的。当与master最后一次交互时间大于指定时间的时候，可以设置slave不进行故障转移
# 格式如下: 
#
#   (node-timeout * replica-validity-factor) + repl-ping-replica-period
#
# So for example if node-timeout is 30 seconds, and the replica-validity-factor
# is 10, and assuming a default repl-ping-replica-period of 10 seconds, the
# replica will not try to failover if it was not able to talk with the master
# for longer than 310 seconds.
# 例如 node-timeout为30秒，replica-validity-factor为10，假设repl-ping-replica-period为10
# 则slave与master上次的交互时间在310秒后，出现故障不会进行故障转移
#
# A large replica-validity-factor may allow replicas with too old data to failover
# a master, while a too small value may prevent the cluster from being able to
# elect a replica at all.
# replica-validity-factor设置太大，会运行太旧的数据进行故障转移。
# 设置太小，则会影响选一个slave进行故障转移
#
# For maximum availability, it is possible to set the replica-validity-factor
# to a value of 0, which means, that replicas will always try to failover the
# master regardless of the last time they interacted with the master.
# (However they'll always try to apply a delay proportional to their
# offset rank).
# replica-validity-factor设置为0表示永远都会故障转移，不在乎slave和master最后交互时间
# (然后故障转移取决于延迟比例和偏移量排名)
#
# Zero is the only value able to guarantee that when all the partitions heal
# the cluster will always be able to continue.
# 0是唯一能够保证所有分区恢复时，集群能够继续运行的
#
# cluster-replica-validity-factor 10

# Cluster replicas are able to migrate to orphaned masters, that are masters
# that are left without working replicas. This improves the cluster ability
# to resist to failures as otherwise an orphaned master can't be failed over
# in case of failure if it has no working replicas.
# slave集群是能够迁移到单独的master的，即master可以没有slave。这样提高了集群抵抗失败的能力，
# 否则没有slave，一个单独的master是不能失败的
#
# Replicas migrate to orphaned masters only if there are still at least a
# given number of other working replicas for their old master. This number
# is the "migration barrier". A migration barrier of 1 means that a replica
# will migrate only if there is at least 1 other working replica for its master
# and so forth. It usually reflects the number of replicas you want for every
# master in your cluster.
# 只有存在至少一个slave为了master时才能迁移，这叫做"迁移屏障"
# 
#
# Default is 1 (replicas migrate only if their masters remain with at least
# one replica). To disable migration just set it to a very large value.
# A value of 0 can be set but is useful only for debugging and dangerous
# in production.
# 默认是1(至少要有一个slave)，设置非常的数字表示禁止。设置为0使用于debug，但对生产来说很危险
#
# cluster-migration-barrier 1

# By default Redis Cluster nodes stop accepting queries if they detect there
# is at least an hash slot uncovered (no available node is serving it).
# This way if the cluster is partially down (for example a range of hash slots
# are no longer covered) all the cluster becomes, eventually, unavailable.
# It automatically returns available as soon as all the slots are covered again.
# 默认情况下，Redis集群节点在检测到查询未覆盖hash slot时(没有可用节点为其提供服务)，会停止接受查询
# 如果集群部分关闭(例如一系列hash slot不再覆盖)所有集群最终变得不可用。
# 一旦所有hash slot都被覆盖，它就会自动返回可用。
#
# However sometimes you want the subset of the cluster which is working,
# to continue to accept queries for the part of the key space that is still
# covered. In order to do so, just set the cluster-require-full-coverage
# option to no.
# 然而有时你想集群的子集仍然可以继续工作，你可以通过cluster-require-full-coverage设置
#
# cluster-require-full-coverage yes

# This option, when set to yes, prevents replicas from trying to failover its
# master during master failures. However the master can still perform a
# manual failover, if forced to do so.
# 当设置为"yes"， 防止master故障转移，但是master仍然可以手动设置(强制)
#
# This is useful in different scenarios, especially in the case of multiple
# data center operations, where we want one side to never be promoted if not
# in the case of a total DC failure.
# 在不同的情况下这是很有用的，特别是在多个数据中心运营，在DC故障的情况下，我们希望一方永远不会被提升。
#
# cluster-replica-no-failover no

# This option, when set to yes, allows nodes to serve read traffic while the
# the cluster is in a down state, as long as it believes it owns the slots. 
# 当设置为yes时，此选项允许节点在只要集群认为自己拥有插槽，它就处于关闭状态。
#
# This is useful for two cases.  The first case is for when an application 
# doesn't require consistency of data during node failures or network partitions.
# One example of this is a cache, where as long as the node has the data it
# should be able to serve it. 
# 这对两种情况很有用。第一种情况是当一个应用程序在节点故障或网络分区期间不需要数据的一致性。
# 其中一个例子是缓存，只要节点拥有数据应该可以为它服务。
#
# The second use case is for configurations that don't meet the recommended  
# three shards but want to enable cluster mode and scale later. A 
# master outage in a 1 or 2 shard configuration causes a read/write outage to the
# entire cluster without this option set, with it set there is only a write outage.
# Without a quorum of masters, slot ownership will not change automatically. 
# 第二种情况是，用于配置不符合推荐的3个碎片，但希望以后启用群集模式和缩放。
# 1 或 2个碎片配置会导致整个集群中，没有写/读中断这个选项，设置了就只有写中断。
# 如果master没有的法定人数，插槽所有权( slot ownership)将不会自动更改。
#
# cluster-allow-reads-when-down no

# In order to setup your cluster make sure to read the documentation
# available at http://redis.io web site.
```

### CLUSTER DOCKER/NAT support DOCKER/NAT方式集群支持

```text
########################## CLUSTER DOCKER/NAT support  ########################
# DOCKER/NAT方式集群支持
#
# In certain deployments, Redis Cluster nodes address discovery fails, because
# addresses are NAT-ted or because ports are forwarded (the typical case is
# Docker and other containers).
# 在某些部署中，Redis群集节点地址发现失败，因为地址是NAT-ted或因为端口是转发的(典型的情况是Docker或其他容器)
#
# In order to make Redis Cluster working in such environments, a static
# configuration where each node knows its public address is needed. The
# following two options are used for this scope, and are:
# 为了让Redis集群在这样的环境(Docker或其他容器)运行，要为每个节点一个静态的配置，需要配置以下内容:
#
# * cluster-announce-ip
# * cluster-announce-port
# * cluster-announce-bus-port
#
# Each instruct the node about its address, client port, and cluster message
# bus port. The information is then published in the header of the bus packets
# so that other nodes will be able to correctly map the address of the node
# publishing the information.
#
# 每个节点都指示其地址、客户端端口和群集消息、总线端口。信息随后发布在总线数据包的报头中，
# 以便其他节点能够正确映射节点的地址
#
# If the above options are not used, the normal Redis Cluster auto-detection
# will be used instead.
# 如果下面的选项没有被使用，Redis会自动发现并使用
#
# Note that when remapped, the bus port may not be at the fixed offset of
# clients port + 10000, so you can specify any port and bus-port depending
# on how they get remapped. If the bus-port is not set, a fixed offset of
# 10000 will be used as usually.
#
# Example:
#
# cluster-announce-ip 10.1.1.5
# cluster-announce-port 6379
# cluster-announce-bus-port 6380
```

### SLOW LOG 慢查询日志

```text
################################## SLOW LOG ###################################
# 慢查询日志
#
# The Redis Slow Log is a system to log queries that exceeded a specified
# execution time. The execution time does not include the I/O operations
# like talking with the client, sending the reply and so forth,
# but just the time needed to actually execute the command (this is the only
# stage of command execution where the thread is blocked and can not serve
# other requests in the meantime).
# Redis Slow Log 记录超过特定执行时间的命令。执行时间不包括I/O计算比如连接客户端，返回结果等，只是命令执行时间
# (这是执行命令的阶段，其他线程被阻塞，无法提供同时提供其他服务的时间段)
#
# You can configure the slow log with two parameters: one tells Redis
# what is the execution time, in microseconds, to exceed in order for the
# command to get logged, and the other parameter is the length of the
# slow log. When a new command is logged the oldest one is removed from the
# queue of logged commands.
# 你可以配置slow log两个参数: 
# 一个是告诉Redis执行超过多少时间被记录的参数slowlog-log-slower-than(微秒)，
# 另一个是slow log 的长度。当一个新命令被记录的时候最早的命令将被从队列中移除

# The following time is expressed in microseconds, so 1000000 is equivalent
# to one second. Note that a negative number disables the slow log, while
# a value of zero forces the logging of every command.
# 下面的时间以微秒为单位，因此1000000代表一秒钟。
# 注意，设置为负数将关闭慢日志，而设置为0将强制每个命令都会记录
slowlog-log-slower-than 10000

# There is no limit to this length. Just be aware that it will consume memory.
# You can reclaim memory used by the slow log with SLOWLOG RESET.
# 对日志长度没有限制，只是要注意它会消耗内存
# 可以通过 SLOWLOG RESET 回收被慢查询日志消耗的内存
slowlog-max-len 128
```

### LATENCY MONITOR 延迟监视器

```text
################################ LATENCY MONITOR ##############################
# 延迟监视器
#
# The Redis latency monitoring subsystem samples different operations
# at runtime in order to collect data related to possible sources of
# latency of a Redis instance.
# Redis延迟监控器子系统，对运行的时的不同操作进行记录，以便收集与Redis实例的延迟
#
# Via the LATENCY command this information is available to the user that can
# print graphs and obtain reports.
# 用LATENCY命令可以打印Redis在运行命令时的耗时图表。
#
# The system only logs operations that were performed in a time equal or
# greater than the amount of milliseconds specified via the
# latency-monitor-threshold configuration directive. When its value is set
# to zero, the latency monitor is turned off.
# 只记录 >= 下面设置的值(latency-monitor-threshold)的操作。
# 0的话，就是关闭监视。


#
# By default latency monitoring is disabled since it is mostly not needed
# if you don't have latency issues, and collecting data has a performance
# impact, that while very small, can be measured under big load. Latency
# monitoring can easily be enabled at runtime using the command
# "CONFIG SET latency-monitor-threshold <milliseconds>" if needed.
# 默认是关闭的，因为大部分是不需要的。
# 可以在运行时动态开启，直接使用CONFIG SET latency-monitor-threshold <milliseconds>命令设置即可
latency-monitor-threshold 0
```

### EVENT NOTIFICATION 事件通知

```text
############################# EVENT NOTIFICATION ##############################
# 事件通知
# 
# Redis can notify Pub/Sub clients about events happening in the key space.
# This feature is documented at http://redis.io/topics/notifications
# Redis可以通过发布/订阅的客户端
#
# For instance if keyspace events notification is enabled, and a client
# performs a DEL operation on key "foo" stored in the Database 0, two
# messages will be published via Pub/Sub:
# 例如，开启了事件通知，则当客户端在数据库 0 删除key "foo"时，会通过Pub/Sub发布消息，如下:
#
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
#
# It is possible to select the events that Redis will notify among a set
# of classes. Every class is identified by a single character:
# 可以在一个类中选择 哪些集合 需要通知的事件，每个类由一个标识符组成:
#
#  K     Keyspace events, published with __keyspace@<db>__ prefix.
#  E     Keyevent events, published with __keyevent@<db>__ prefix.
#  g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
#  $     String commands
#  l     List commands
#  s     Set commands
#  h     Hash commands
#  z     Sorted set commands
#  x     Expired events (events generated every time a key expires)
#  e     Evicted events (events generated when a key is evicted for maxmemory)
#  t     Stream commands
#  m     Key-miss events (Note: It is not included in the 'A' class)
#  A     Alias for g$lshzxet, so that the "AKE" string means all the events
#        (Except key-miss events which are excluded from 'A' due to their
#         unique nature).
#
#  The "notify-keyspace-events" takes as argument a string that is composed
#  of zero or multiple characters. The empty string means that notifications
#  are disabled.
#
#  Example: to enable list and generic events, from the point of view of the
#           event name, use:
#
#  notify-keyspace-events Elg
#
#  Example 2: to get the stream of the expired keys subscribing to channel
#             name __keyevent@0__:expired use:
#
#  notify-keyspace-events Ex
#
#  By default all notifications are disabled because most users don't need
#  this feature and the feature has some overhead. Note that if you don't
#  specify at least one of K or E, no events will be delivered.
#  大部分人不需要这个功能，并且还需要一定开销，所以默认关闭。
notify-keyspace-events ""
```

### GOPHER SERVER 地图服务器

```text
############################### GOPHER SERVER #################################
# 地图服务器
# 
# Redis contains an implementation of the Gopher protocol, as specified in
# the RFC 1436 (https://www.ietf.org/rfc/rfc1436.txt).
# Redis实现了地图协议，你可以在RFC 1436中查看 (https://www.ietf.org/rfc/rfc1436.txt).
#
# The Gopher protocol was very popular in the late '90s. It is an alternative
# to the web, and the implementation both server and client side is so simple
# that the Redis server has just 100 lines of code in order to implement this
# support.
# Gopher协议在90年代是非常有名。Redis支持其，实现只用了100行代码
#
# What do you do with Gopher nowadays? Well Gopher never *really* died, and
# lately there is a movement in order for the Gopher more hierarchical content
# composed of just plain text documents to be resurrected. Some want a simpler
# internet, others believe that the mainstream internet became too much
# controlled, and it's cool to create an alternative space for people that
# want a bit of fresh air.
# 现在Gopher怎么样了？当然，Gopher从来没有真的消息
#
# Anyway for the 10nth birthday of the Redis, we gave it the Gopher protocol
# as a gift.
# 作为Redis十周年的生日，我们实现了Gopher作为一份礼物
#
# --- HOW IT WORKS? ---
# --- 如何工作? ---
#
# The Redis Gopher support uses the inline protocol of Redis, and specifically
# two kind of inline requests that were anyway illegal: an empty request
# or any request that starts with "/" (there are no Redis commands starting
# with such a slash). Normal RESP2/RESP3 requests are completely out of the
# path of the Gopher protocol implementation and are served as usually as well.
#
# If you open a connection to Redis when Gopher is enabled and send it
# a string like "/foo", if there is a key named "/foo" it is served via the
# Gopher protocol.
#
# In order to create a real Gopher "hole" (the name of a Gopher site in Gopher
# talking), you likely need a script like the following:
# 要创建"hole"地图的话，需要编写脚本，请参考下面github链接:
#
#   https://github.com/antirez/gopher2redis
#
# --- SECURITY WARNING ---
# --- 安全 警告 ---
#
# If you plan to put Redis on the internet in a publicly accessible address
# to server Gopher pages MAKE SURE TO SET A PASSWORD to the instance.
# Once a password is set:
# 如果你要把Gopher发布到网上，请一定要设置密码
#
#   1. The Gopher server (when enabled, not by default) will still serve
#      content via Gopher.
#   2. However other commands cannot be called before the client will
#      authenticate.
#
# So use the 'requirepass' option to protect your instance.
# 设置密码请查看'requirepass'
#
# To enable Gopher support uncomment the following line and set
# the option from no (the default) to yes.
# 默认是 no，开启需设置为yes
#
# gopher-enabled no
```

### ADVANCED CONFIG 高级配置

```text
############################### ADVANCED CONFIG ###############################
# 高级配置
#
# Hashes are encoded using a memory efficient data structure when they have a
# small number of entries, and the biggest entry does not exceed a given
# threshold. These thresholds can be configured using the following directives.
# 当hash中包含超过指定元素个数 并且 最大的元素 没有超过临界(threshold)时，
# hash将以一种特殊的编码方式（大大减少内存使用）来存储，这里可以设置这两个临界值
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

# Lists are also encoded in a special way to save a lot of space.
# The number of entries allowed per internal list node can be specified
# as a fixed maximum size or a maximum number of elements.
# For a fixed maximum size, use -5 through -1, meaning:
# List集合也是一种存储方式，内部的元素存储可以指定一个最大的空间。
# 设置最大长度，通过-5 ~ -1 去设置，解释如下:
# -5: max size: 64 Kb  <-- not recommended for normal workloads 不建议用于正常的工作负载
# -4: max size: 32 Kb  <-- not recommended 不建议
# -3: max size: 16 Kb  <-- probably not recommended 可能不建议
# -2: max size: 8 Kb   <-- good 好
# -1: max size: 4 Kb   <-- good 好
# Positive numbers mean store up to _exactly_ that number of elements
# per list node.
# 正确的数量意味着更能存储真正的每个元素
# The highest performing option is usually -2 (8 Kb size) or -1 (4 Kb size),
# but if your use case is unique, adjust the settings as necessary.
# 最好的性能是使用 -2 (8 Kb size) or -1 (4 Kb size)，但这不是唯一的，你可以根据需要调整
list-max-ziplist-size -2

# Lists may also be compressed.
# Compress depth is the number of quicklist ziplist nodes from *each* side of
# the list to *exclude* from compression.  The head and tail of the list
# are always uncompressed for fast push/pop operations.  Settings are:
# List可能会被压缩。压缩的深度取决于每个( *each*)快速压缩列表的节点和排除(*exclude*)。
# 头和尾永远不会被压缩，为了能够快速的查询和删除(push/pop)，设置如下:
# 0: disable all list compression
# 1: depth 1 means "don't start compressing until after 1 node into the list,
#    going from either the head or tail"
#    So: [head]->node->node->...->node->[tail]
#    [head], [tail] will always be uncompressed; inner nodes will compress.
# 2: [head]->[next]->node->node->...->node->[prev]->[tail]
#    2 here means: don't compress head or head->next or tail->prev or tail,
#    but compress all nodes between them.
# 3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]
# etc.
list-compress-depth 0

# Sets have a special encoding in just one case: when a set is composed
# of just strings that happen to be integers in radix 10 in the range
# of 64 bit signed integers.
# The following configuration setting sets the limit in the size of the
# set in order to use this special memory saving encoding.
# Set集合是一种特殊的编码方式：当集合组合的范围，正好是基数10且是64位有符号整数时。
# 可以使用下面的配置限制其大小
set-max-intset-entries 512

# Similarly to hashes and lists, sorted sets are also specially encoded in
# order to save a lot of space. This encoding is only used when the length and
# elements of a sorted set are below the following limits:
# 和hash、list差不多，zset是一种特殊的编码方式。
# 译者注:
# zsort数据类型多少节点以下 会采用去指针的紧凑存储格式。
# zsort数据类型节点值大小 小于多少字节会采用紧凑存储格式。
zset-max-ziplist-entries 128
zset-max-ziplist-value 64

# HyperLogLog sparse representation bytes limit. The limit includes the
# 16 bytes header. When an HyperLogLog using the sparse representation crosses
# this limit, it is converted into the dense representation.
# HyperLogLog 稀疏表示字节数限制
# 限制头为16字节。当使用稀疏表示的HyperLogLog交叉这个极限，它被转换成稠密表示。
#
# A value greater than 16000 is totally useless, since at that point the
# dense representation is more memory efficient.
# 大于16000的值是完全无用的，因为此时密集表示更节省内存。
#
# The suggested value is ~ 3000 in order to have the benefits of
# the space efficient encoding without slowing down too much PFADD,
# which is O(N) with the sparse encoding. The value can be raised to
# ~ 10000 when CPU is not a concern, but space is, and the data set is
# composed of many HyperLogLogs with cardinality in the 0 - 15000 range.
# 默认是3000，当CPU很好的话，可以设置为10000
hll-sparse-max-bytes 3000

# Streams macro node max size / items. The stream data structure is a radix
# tree of big nodes that encode multiple items inside. Using this configuration
# it is possible to configure how big a single node can be in bytes, and the
# maximum number of items it may contain before switching to a new node when
# appending new stream entries. If any of the following settings are set to
# zero, the limit is ignored, so for instance it is possible to set just a
# max entires limit by setting max-bytes to 0 and max-entries to the desired
# value.
stream-node-max-bytes 4096
stream-node-max-entries 100

# Active rehashing uses 1 millisecond every 100 milliseconds of CPU time in
# order to help rehashing the main Redis hash table (the one mapping top-level
# keys to values). The hash table implementation Redis uses (see dict.c)
# performs a lazy rehashing: the more operation you run into a hash table
# that is rehashing, the more rehashing "steps" are performed, so if the
# server is idle the rehashing is never complete and some more memory is used
# by the hash table.
# Redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用
#
# The default is to use this millisecond 10 times every second in order to
# actively rehash the main dictionaries, freeing memory when possible.
#
# If unsure:
# use "activerehashing no" if you have hard latency requirements and it is
# not a good thing in your environment that Redis can reply from time to time
# to queries with 2 milliseconds delay.
#
# use "activerehashing yes" if you don't have such hard requirements but
# want to free memory asap when possible.
# 如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存
activerehashing yes

# The client output buffer limits can be used to force disconnection of clients
# that are not reading data from the server fast enough for some reason (a
# common reason is that a Pub/Sub client can't consume messages as fast as the
# publisher can produce them).
# 客户端输出缓冲区限制 可用于强制断开客户端连接
# 由于某种原因，从服务器读取数据的速度不够快(常见的原因是Pub/Sub客户端不能像生产者发布那样快的去消费)
#
# The limit can be set differently for the three different classes of clients:
# 有三种不同方式设置客户端:
# normal -> normal clients including MONITOR clients 正常的客户端，包括监控客户端
# replica  -> replica clients  从机，slave客户端
# pubsub -> clients subscribed to at least one pubsub channel or pattern 至少一个订阅的的客户端
#
# The syntax of every client-output-buffer-limit directive is the following:
# client-output-buffer-limit设置如下:
# 译者注: 可以用来强制关闭传输缓慢的客户端(比如redis pub的东西有比较慢的client无法及时sub)
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
#
# A client is immediately disconnected once the hard limit is reached, or if
# the soft limit is reached and remains reached for the specified number of
# seconds (continuously).
# So for instance if the hard limit is 32 megabytes and the soft limit is
# 16 megabytes / 10 seconds, the client will get disconnected immediately
# if the size of the output buffers reach 32 megabytes, but will also get
# disconnected if the client reaches 16 megabytes and continuously overcomes
# the limit for 10 seconds.
# 当hard限制到了会立即被关闭客户端。如果soft限制到了，会等soft秒。
# 比如硬限制是32m，soft是16m，10secs。到32m就立即断，或者在16m以上停止了10secs。
#
# By default normal clients are not limited because they don't receive data
# without asking (in a push way), but just after a request, so only
# asynchronous clients may create a scenario where data is requested faster
# than it can read.
#
# Instead there is a default limit for pubsub and replica clients, since
# subscribers and replicas receive data in a push fashion.
#
# Both the hard or the soft limit can be disabled by setting them to zero.
# 设置成0就是关闭
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

# Client query buffers accumulate new commands. They are limited to a fixed
# amount by default in order to avoid that a protocol desynchronization (for
# instance due to a bug in the client) will lead to unbound memory usage in
# the query buffer. However you can configure it here if you have very special
# needs, such us huge multi/exec requests or alike.
#
# client-query-buffer-limit 1gb

# In the Redis protocol, bulk requests, that are, elements representing single
# strings, are normally limited ot 512 mb. However you can change this limit
# here.
#
# proto-max-bulk-len 512mb

# Redis calls an internal function to perform many background tasks, like
# closing connections of clients in timeout, purging expired keys that are
# never requested, and so forth.
#
# Not all tasks are performed with the same frequency, but Redis checks for
# tasks to perform according to the specified "hz" value.
#
# By default "hz" is set to 10. Raising the value will use more CPU when
# Redis is idle, but at the same time will make Redis more responsive when
# there are many keys expiring at the same time, and timeouts may be
# handled with more precision.
#
# The range is between 1 and 500, however a value over 100 is usually not
# a good idea. Most users should use the default of 10 and raise this up to
# 100 only in environments where very low latency is required.
# redis内部调度(进行关闭timeout的客户端，删除过期key等等)频率，越大则调度频率越高。
# 设置成100以上会对CPU造成大压力除非你对线上实时性要求很高。可以在1~500之间。
hz 10

# Normally it is useful to have an HZ value which is proportional to the
# number of clients connected. This is useful in order, for instance, to
# avoid too many clients are processed for each background task invocation
# in order to avoid latency spikes.
#
# Since the default HZ value by default is conservatively set to 10, Redis
# offers, and enables by default, the ability to use an adaptive HZ value
# which will temporary raise when there are many connected clients.
#
# When dynamic HZ is enabled, the actual configured HZ will be used
# as a baseline, but multiples of the configured HZ value will be actually
# used as needed once more clients are connected. In this way an idle
# instance will use very little CPU time while a busy instance will be
# more responsive.
dynamic-hz yes

# When a child rewrites the AOF file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
# 当child进程在rewrite AOF文件，如果这个选项是yes，那么这个file每32MB会写fsync()。这个是保证增量写硬盘而防止写硬盘时I/O突增。
aof-rewrite-incremental-fsync yes

# When redis saves RDB file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
# 当redis保存RDB文件时，如果启用了这个选项，那么这个file每32MB会写fsync()。这个是保证增量写硬盘而防止写硬盘时I/O突增。
rdb-save-incremental-fsync yes

# Redis LFU eviction (see maxmemory setting) can be tuned. However it is a good
# idea to start with the default settings and only change them after investigating
# how to improve the performances and how the keys LFU change over time, which
# is possible to inspect via the OBJECT FREQ command.
# LFU移除策略是可以调整的
#
# There are two tunable parameters in the Redis LFU implementation: the
# counter logarithm factor and the counter decay time. It is important to
# understand what the two parameters mean before changing them.
# 有两个调整参数，一个是对数因子和衰退时间，在修改之前你需要阅读下面以理解参数的意思
#
# The LFU counter is just 8 bits per key, it's maximum value is 255, so Redis
# uses a probabilistic increment with logarithmic behavior. Given the value
# of the old counter, when a key is accessed, the counter is incremented in
# this way:
# LFU counter每一个key是8位的，最大255。所以Redis采用probabilistic increment with logarithmic behavior
# 给每一个值一个old counter，当一个key被处理时，counter的增加方式有以下几种:
#
# 1. A random number R between 0 and 1 is extracted.
# 2. A probability P is calculated as 1/(old_value*lfu_log_factor+1).
# 3. The counter is incremented only if R < P.
# 1. 使用随机函数从0~1中提取
# 2. 一个可能性值P = 1/(old_value*lfu_log_factor+1).
# 3. 如果 R < P 则增加
# 
# The default lfu-log-factor is 10. This is a table of how the frequency
# counter changes with a different number of accesses with different
# logarithmic factors:
# lfu-log-factor默认是10，这个表描述了在不同处理时，counter被改变的频率
#
# +--------+------------+------------+------------+------------+------------+
# | factor | 100 hits   | 1000 hits  | 100K hits  | 1M hits    | 10M hits   |
# +--------+------------+------------+------------+------------+------------+
# | 0      | 104        | 255        | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 1      | 18         | 49         | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 10     | 10         | 18         | 142        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 100    | 8          | 11         | 49         | 143        | 255        |
# +--------+------------+------------+------------+------------+------------+
#
# NOTE: The above table was obtained by running the following commands:
# 注意: 上面表格数据是通过下面命令获得的
#
#   redis-benchmark -n 1000000 incr foo
#   redis-cli object freq foo
#
# NOTE 2: The counter initial value is 5 in order to give new objects a chance
# to accumulate hits.
# 注意: counter初始值为5
#
# The counter decay time is the time, in minutes, that must elapse in order
# for the key counter to be divided by two (or decremented if it has a value
# less <= 10).
# counter decay time是指按顺序必须经过的时间(以分钟为单位)，除以2(如果有值，则递减 <= 10)
#
# The default value for the lfu-decay-time is 1. A Special value of 0 means to
# decay the counter every time it happens to be scanned.
# lfu-decay-time默认是1，0意味着每次扫描时都会衰退
#
# lfu-log-factor 10
# lfu-decay-time 1
```

### ACTIVE DEFRAGMENTATION 激活碎片整理

```text
########################### ACTIVE DEFRAGMENTATION #######################
# 激活碎片整理
# What is active defragmentation?
# -------------------------------
#
# Active (online) defragmentation allows a Redis server to compact the
# spaces left between small allocations and deallocations of data in memory,
# thus allowing to reclaim back memory.
#
# Fragmentation is a natural process that happens with every allocator (but
# less so with Jemalloc, fortunately) and certain workloads. Normally a server
# restart is needed in order to lower the fragmentation, or at least to flush
# away all the data and create it again. However thanks to this feature
# implemented by Oran Agra for Redis 4.0 this process can happen at runtime
# in an "hot" way, while the server is running.
#
# Basically when the fragmentation is over a certain level (see the
# configuration options below) Redis will start to create new copies of the
# values in contiguous memory regions by exploiting certain specific Jemalloc
# features (in order to understand if an allocation is causing fragmentation
# and to allocate it in a better place), and at the same time, will release the
# old copies of the data. This process, repeated incrementally for all the keys
# will cause the fragmentation to drop back to normal values.
#
# Important things to understand:
#
# 1. This feature is disabled by default, and only works if you compiled Redis
#    to use the copy of Jemalloc we ship with the source code of Redis.
#    This is the default with Linux builds.
#
# 2. You never need to enable this feature if you don't have fragmentation
#    issues.
#
# 3. Once you experience fragmentation, you can enable this feature when
#    needed with the command "CONFIG SET activedefrag yes".
#
# The configuration parameters are able to fine tune the behavior of the
# defragmentation process. If you are not sure about what they mean it is
# a good idea to leave the defaults untouched.

# Enabled active defragmentation
# activedefrag no

# Minimum amount of fragmentation waste to start active defrag
# active-defrag-ignore-bytes 100mb

# Minimum percentage of fragmentation to start active defrag
# active-defrag-threshold-lower 10

# Maximum percentage of fragmentation at which we use maximum effort
# active-defrag-threshold-upper 100

# Minimal effort for defrag in CPU percentage, to be used when the lower
# threshold is reached
# active-defrag-cycle-min 1

# Maximal effort for defrag in CPU percentage, to be used when the upper
# threshold is reached
# active-defrag-cycle-max 25

# Maximum number of set/hash/zset/list fields that will be processed from
# the main dictionary scan
# active-defrag-max-scan-fields 1000

# Jemalloc background thread for purging will be enabled by default
jemalloc-bg-thread yes

# It is possible to pin different threads and processes of Redis to specific
# CPUs in your system, in order to maximize the performances of the server.
# This is useful both in order to pin different Redis threads in different
# CPUs, but also in order to make sure that multiple Redis instances running
# in the same host will be pinned to different CPUs.
#
# Normally you can do this using the "taskset" command, however it is also
# possible to this via Redis configuration directly, both in Linux and FreeBSD.
#
# You can pin the server/IO threads, bio threads, aof rewrite child process, and
# the bgsave child process. The syntax to specify the cpu list is the same as
# the taskset command:
#
# Set redis server/io threads to cpu affinity 0,2,4,6:
# server_cpulist 0-7:2
#
# Set bio threads to cpu affinity 1,3:
# bio_cpulist 1,3
#
# Set aof rewrite child process to cpu affinity 8,9,10,11:
# aof_rewrite_cpulist 8-11
#
# Set bgsave child process to cpu affinity 1,10,11
# bgsave_cpulist 1,10-11
```

