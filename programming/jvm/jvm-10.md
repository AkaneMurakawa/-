# 10 问题排查

## 常用整理

* 查看服务负载
* 查看错误日志（error.log）
* 查看gc、heap情况
* dump文件分析

```text
# 查看系统负载load average:1分钟 5分钟 15分钟
watch
w  # show who
wath -t uptime
uptime
top 


# 显示或管理执行中的程序
top -Hbn 1 | grep java
top -Hp
# -H：（Threads mode）设置线程模式，不带-H显示的是总和，带-H显示的内容更多。
# -b：（Batch-mode operation）以批处理模式操作。在此模式，可进行输出。
# -p：(Monitor-PIDs mode) 指定进程，显示进程信息
交互操作
# d或s：屏幕刷新间隔时间，单位s。输入0值则系统将不断刷新，默认值是3s【常用】
# top 按键：M P T 通常我们会查看占用内存或者CPU，看看服务负载是否高


ps -ef | grep java
ps -aux
# ps -ef 以system v方式列出当前全部运行的进程
# ps -aux 以BSD方式列出当前全部运行的进程

# 查看cpu硬件新
lscpu

# 查看系统存储
free -h
df -h
df -lh
du -h

# 查看最繁忙服务
top -bn 1 | grep java
top -bn 1 | grep java | head -n1 | awk '{ print $1}'

# 越过错误
# 错误一：命令 `sudo` 是被禁止的 ...
# 错误二：Attaching to process ID 21245, please wait...
i=21245;
sudo $JAVA_HOME/bin/jmap -heap $i


# 查看最繁忙的线程
processid=$(top -bn 1 | grep java | head -n1 | awk '{ print $1}');
threadid=$(top -Hbn 1 -p ${processid} | grep java | head -n1 | awk '{ print $1}' | xargs printf  '%x');
sudo $JAVA_HOME/bin/jstack $processid | grep "0x$threadid" -A100 --color;


# 查看最繁忙进程的GC情况
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jstat -gc $processid 3000 2;


# 查看最繁忙进程的heap情况
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jmap -heap $processid;


# 查看最繁忙进程的 对象情况
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jmap -histo $processid | head -200;

# dump文件
jmap –dump:format=b,file=/tmp/xxx.hprof pid

# mat / jhat分析
```

