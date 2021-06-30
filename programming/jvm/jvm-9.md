# 9 Arthas

## 介绍

`Arthas` 是Alibaba开源的Java诊断工具

[https://arthas.aliyun.com/doc/](https://arthas.aliyun.com/doc/)

为什么需要在线排查？ 在生产上我们经常会碰到一些不好排查的问题，例如线程安全问题，用最简单的threaddump或者heapdump不好查到问题原因。为了排查这些问题，有时我们会临时加一些日志，比如在一些关键的函数里打印出入参，然后重新打包发布，如果打了日志还是没找到问题，继续加日志，重新打包发布。对于上线流程复杂而且审核比较严的公司，从改代码到上线需要层层的流转，会大大影响问题排查的进度。

* jvm：观察jvm信息
* thread：定位线程问题
* dashboard：观察系统情况
* heapdump: 自动dump + jhat：分析
* jad：反编译 
  * 动态代理生成类的问题定位 
  * 第三方的类（观察代码） 
  * 版本问题（确定自己最新提交的版本是不是被使用）
* redefine：热替换 （ClassLoader）
  * 目前有些限制条件：只能改方法实现（方法已经运行完成），不能改方法名， 不能改属性 m\(\) -&gt; mm\(\)
* sc - search class
* watch - watch method
* 没有包含的功能：jmap

