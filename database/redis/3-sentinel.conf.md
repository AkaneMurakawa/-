# 3 sentinel.conf中文翻译

```text
# Example sentinel.conf
# 哨兵配置

# *** IMPORTANT ***
#
# By default Sentinel will not be reachable from interfaces different than
# localhost, either use the 'bind' directive to bind to a list of network
# interfaces, or disable protected mode with "protected-mode no" by
# adding it to this configuration file.
# 默认情况下，哨兵将无法通过不同于以下接口的接口访问localhost，可以使用'bind'指令绑定到网络列表
# 或禁用保护模式: "protected-mode no"
#
# Before doing that MAKE SURE the instance is protected from the outside
# world via firewalling or other means.
# 在你确定系统有防火墙保护时才设置下面的bind
#
# For example you may use one of the following:
#
# bind 127.0.0.1 192.168.1.1
#
# 保护模式
# protected-mode no

# port <sentinel-port>
# The port that this sentinel instance will run on
# 哨兵运行的端口
port 26379

# By default Redis Sentinel does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis-sentinel.pid when
# daemonized.
# 默认Redis哨兵不是后台运行的，如果你想启用可以设置为 'yes'。记住如果是后台运行Redis会将pid写入到
# /var/run/redis-sentinel.pid文件中。
daemonize no

# When running daemonized, Redis Sentinel writes a pid file in
# /var/run/redis-sentinel.pid by default. You can specify a custom pid file
# location here.
# 如果是后台运行，Redis会将pid写入到/var/run/redis-sentinel.pid文件中，你可以自己指定
pidfile /var/run/redis-sentinel.pid

# Specify the log file name. Also the empty string can be used to force
# Sentinel to log on the standard output. Note that if you use standard
# output for logging but daemonize, logs will be sent to /dev/null
# 日志文件路径，如果没有指定，会默认标准输出到 /dev/null
logfile ""

# sentinel announce-ip <ip>
# sentinel announce-port <port>
#
# The above two configuration directives are useful in environments where,
# because of NAT, Sentinel is reachable from outside via a non-local address.
# 这上面两个配置用于指定环境。哨兵可通过非本地地址从外部访问。
#
# When announce-ip is provided, the Sentinel will claim the specified IP address
# in HELLO messages used to gossip its presence, instead of auto-detecting the
# local address as it usually does.
# 当指定ip时，哨兵会使用指定的ip，而不会自动检测本地 地址
#
# Similarly when announce-port is provided and is valid and non-zero, Sentinel
# will announce the specified TCP port.
# port也类似，port不能指定一个为0的数，没指定Redis会自动检测TCP端口
#
# The two options don't need to be used together, if only announce-ip is
# provided, the Sentinel will announce the specified IP and the server port
# as specified by the "port" option. If only announce-port is provided, the
# Sentinel will announce the auto-detected local IP and the specified port.
# 这两个配置(sentinel announce-ip <ip> 和sentinel announce-port <port>)不需要都指定，
# 如果没指定，Redis会自动检测
#
# Example:
#
# sentinel announce-ip 1.2.3.4

# dir <working-directory>
# Every long running process should have a well-defined working directory.
# For Redis Sentinel to chdir to /tmp at startup is the simplest thing
# for the process to don't interfere with administrative tasks such as
# unmounting filesystems.
# 运行工作空间
dir /tmp

# sentinel monitor <master-name> <ip> <redis-port> <quorum>
#
# Tells Sentinel to monitor this master, and to consider it in O_DOWN
# (Objectively Down) state only if at least <quorum> sentinels agree.
# 上面的配置告诉哨兵要监控的master，如果哨兵投票的结果达到法定人数，则认为master挂掉了
#
# Note that whatever is the ODOWN quorum, a Sentinel will require to
# be elected by the majority of the known Sentinels in order to
# start a failover, so no failover can be performed in minority.
# 无论是否挂掉，都是需要由已知存活的哨兵投票决定的，以便开启故障转移，因此在少数情况下无法进行故障转移。
#
# Replicas are auto-discovered, so you don't need to specify replicas in
# any way. Sentinel itself will rewrite this configuration file adding
# the replicas using additional configuration options.
# Also note that the configuration file is rewritten when a
# replica is promoted to master.
# 从机会自动发现，所以不需要在这里任何从机。哨兵会重写此配置文件，使用添加配置选项，
# 将配置添加到从机。
# 注意当从机变为了master也会重写改配置文件
#
# Note: master name should not include special characters or spaces.
# The valid charset is A-z 0-9 and the three characters ".-_".
# master的名称不能包含特殊字符或空格，只能是 A-z 0-9和".-_"，例如:
sentinel monitor mymaster 127.0.0.1 6379 2

# sentinel auth-pass <master-name> <password>
#
# Set the password to use to authenticate with the master and replicas.
# Useful if there is a password set in the Redis instances to monitor.
# 如果master需要密码，在这里需要设置密码
#
# Note that the master password is also used for replicas, so it is not
# possible to set a different password in masters and replicas instances
# if you want to be able to monitor these instances with Sentinel.
# 记住，master的密码也适用从机，因此master和从机不要设置不同的密码，如果你想哨兵监控的话
#
# However you can have Redis instances without the authentication enabled
# mixed with Redis instances requiring the authentication (as long as the
# password set is the same for all the instances requiring the password) as
# the AUTH command will have no effect in Redis instances with authentication
# switched off.
# AUTH命令在具有身份验证的Redis实例中无效。
#
# Example:
#
# sentinel auth-pass mymaster MySUPER--secret-0123passw0rd

# sentinel auth-user <master-name> <username>
#
# This is useful in order to authenticate to instances having ACL capabilities,
# that is, running Redis 6.0 or greater. When just auth-pass is provided the
# Sentinel instance will authenticate to Redis using the old "AUTH <pass>"
# method. When also an username is provided, it will use "AUTH <user> <pass>".
# In the Redis servers side, the ACL to provide just minimal access to
# Sentinel instances, should be configured along the following lines:
#
#     user sentinel-user >somepassword +client +subscribe +publish \
#                        +ping +info +multi +slaveof +config +client +exec on

# sentinel down-after-milliseconds <master-name> <milliseconds>
#
# Number of milliseconds the master (or any attached replica or sentinel) should
# be unreachable (as in, not acceptable reply to PING, continuously, for the
# specified period) in order to consider it in S_DOWN state (Subjectively
# Down).
# 心跳检测，单位毫秒
#
# Default is 30 seconds.
# 默认30秒
sentinel down-after-milliseconds mymaster 30000

# requirepass <password>
#
# You can configure Sentinel itself to require a password, however when doing
# so Sentinel will try to authenticate with the same password to all the
# other Sentinels. So you need to configure all your Sentinels in a given
# group with the same "requirepass" password. Check the following documentation
# for more info: https://redis.io/topics/sentinel
# 设置密码，但是如果有多个哨兵，你需要设置相同的密码

# sentinel parallel-syncs <master-name> <numreplicas>
#
# How many replicas we can reconfigure to point to the new replica simultaneously
# during the failover. Use a low number if you use the replicas to serve query
# to avoid that all the replicas will be unreachable at about the same
# time while performing the synchronization with the master.
# 当有多个哨兵我们可以设置进行故障转移，这个数字要尽量设置小，避免主机和从机同步期间都挂掉
sentinel parallel-syncs mymaster 1

# sentinel failover-timeout <master-name> <milliseconds>
#
# Specifies the failover timeout in milliseconds. It is used in many ways:
# 设置故障转移超时限制，单位毫秒
#
# - The time needed to re-start a failover after a previous failover was
#   already tried against the same master by a given Sentinel, is two
#   times the failover timeout.
# - 先前的故障转移后，又重新启动故障转移，这个时间是故障转移的两倍
#
# - The time needed for a replica replicating to a wrong master according
#   to a Sentinel current configuration, to be forced to replicate
#   with the right master, is exactly the failover timeout (counting since
#   the moment a Sentinel detected the misconfiguration).
# - 根据以下说明复制到错误的master所需的时间，
#   配置为Sentinel当前配置，必须强制复制与正确的master一起，恰好是故障转移超时
#   (哨兵检测到错误配置的时间)
#
# - The time needed to cancel a failover that is already in progress but
#   did not produced any configuration change (SLAVEOF NO ONE yet not
#   acknowledged by the promoted replica).
# - 取消已进行但取消的故障转移所需的时间，没有进行任何配置更改
#   (使用SLAVEOF NO ONE指令，但从机还没有被提升为master的时间)
#
# - The maximum time a failover in progress waits for all the replicas to be
#   reconfigured as replicas of the new master. However even after this time
#   the replicas will be reconfigured by the Sentinels anyway, but not with
#   the exact parallel-syncs progression as specified.
# - 最长时间是故障转移正在进行中等待所有从机重新配置为新master。 
#   但是在这段时间之后，无论如何，从机将由哨兵重新配置，但不能通过指定的确切并行同步进度。
#
# Default is 3 minutes.
# 默认3分钟
sentinel failover-timeout mymaster 180000

# SCRIPTS EXECUTION
#
# sentinel notification-script and sentinel reconfig-script are used in order
# to configure scripts that are called to notify the system administrator
# or to reconfigure clients after a failover. The scripts are executed
# with the following rules for error handling:
#
# If script exits with "1" the execution is retried later (up to a maximum
# number of times currently set to 10).
#
# If script exits with "2" (or an higher value) the script execution is
# not retried.
#
# If script terminates because it receives a signal the behavior is the same
# as exit code 1.
#
# A script has a maximum running time of 60 seconds. After this limit is
# reached the script is terminated with a SIGKILL and the execution retried.

# NOTIFICATION SCRIPT
#
# sentinel notification-script <master-name> <script-path>
# 
# Call the specified notification script for any sentinel event that is
# generated in the WARNING level (for instance -sdown, -odown, and so forth).
# This script should notify the system administrator via email, SMS, or any
# other messaging system, that there is something wrong with the monitored
# Redis systems.
#
# The script is called with just two arguments: the first is the event type
# and the second the event description.
#
# The script must exist and be executable in order for sentinel to start if
# this option is provided.
#
# Example:
#
# sentinel notification-script mymaster /var/redis/notify.sh

# CLIENTS RECONFIGURATION SCRIPT
#
# sentinel client-reconfig-script <master-name> <script-path>
#
# When the master changed because of a failover a script can be called in
# order to perform application-specific tasks to notify the clients that the
# configuration has changed and the master is at a different address.
# 
# The following arguments are passed to the script:
#
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
#
# <state> is currently always "failover"
# <role> is either "leader" or "observer"
# 
# The arguments from-ip, from-port, to-ip, to-port are used to communicate
# the old address of the master and the new address of the elected replica
# (now a master).
#
# This script should be resistant to multiple invocations.
#
# Example:
#
# sentinel client-reconfig-script mymaster /var/redis/reconfig.sh

# SECURITY
#
# By default SENTINEL SET will not be able to change the notification-script
# and client-reconfig-script at runtime. This avoids a trivial security issue
# where clients can set the script to anything and trigger a failover in order
# to get the program executed.

sentinel deny-scripts-reconfig yes

# REDIS COMMANDS RENAMING
#
# Sometimes the Redis server has certain commands, that are needed for Sentinel
# to work correctly, renamed to unguessable strings. This is often the case
# of CONFIG and SLAVEOF in the context of providers that provide Redis as
# a service, and don't want the customers to reconfigure the instances outside
# of the administration console.
# 有时Redis服务器具有哨兵所需的某些命令，能够正常工作。
# 但有时需要重命名为无法猜测的字符串。
# 通常这种情况是在，Redis提供的命令有CONFIG和SLAVEOF，
# 但不希望客户在外部重新配置
#
# In such case it is possible to tell Sentinel to use different command names
# instead of the normal ones. For example if the master "mymaster", and the
# associated replicas, have "CONFIG" all renamed to "GUESSME", I could use:
#
# SENTINEL rename-command mymaster CONFIG GUESSME
#
# After such configuration is set, every time Sentinel would use CONFIG it will
# use GUESSME instead. Note that there is no actual need to respect the command
# case, so writing "config guessme" is the same in the example above.
#
# SENTINEL SET can also be used in order to perform this configuration at runtime.
#
# In order to set a command back to its original name (undo the renaming), it
# is possible to just rename a command to itsef:
#
# SENTINEL rename-command mymaster CONFIG CONFIG
```

