---
layout: post
title: "Hadoop单机和伪分布式安装记录"
categories: Hadoop
tags: 大数据 Hadoop
---

* content
{:toc}

接近两个月没更博客了，因为心里稍微有点郁闷和烦躁。都到2017年了，总得说些什么吧：2016年技术做得还算可以，主要是前端，提高点效率，稍微上点心就更好了，只是烦心事太多（自我矛盾~）。怎么说呢？希望2017年能找份好的实习和工作，能够再有所突破吧，闲扯这些~~





先从大数据写起，学习大数据最开始的起点是 `Hadoop` 和 `Spark` ，`Hadoop` 是老牌平台工具了，所以试着入手了 `Hadoop` 。

> Hadoop的安装方式有三种，单机模式、伪分布模式和完全分布模式。因为条件有限，所以这里介绍单机模式和伪分布模式。

**安装环境：**

* Ubuntu 16.04 X64
* Hadoop 2.7.3

# 准备

## 创建Hadoop用户

建议在 `Linux` 下创建一个新用户，如下：

```shell
    $ sudo useradd -m hadoop -s /bin/bash
```

为用户设置密码：

```shell
    $ sudo passwd hadoop
```

增加管理员权限：

```shell
    $ sudo adduser hadoop sudo
```

## 安装SSH，配置无密码登录S SH

`Ubuntu` 自带 SSH Client ，这里只需安装 Server：

```shell
    $ sudo apt install openssh-server
```

之后可以用以下命令登录：

```shell
    $ ssh localhost
```

但是每次登录都得输入密码，所以需要配置无密登录：

```shell
    $ cd ~/.ssh/
    $ ssh-keygen -t rsa
    $ cat ./id_rsa.pub >> ./authorized_keys
```

这样就配置好了

## 安装Java

这里选择的是到 `Oracle` 官网下载的 `JDK8_112` ，然后自己安装的方式：

下载好压缩包之后，解压到我们一个想放置的目录：

```shell
    tar -zxvf jdk8_112.tar.gz -C /usr/local
```

然后在家目录中配置相应的环境变量：

```shell
    $ vi ~/.bashrc
```

在最上面加上一句：

```shell
    export JAVA_HOME=/usr/local/jdk8_112
```

保存退出，使变量生效：

```shell
    $ source ~/.bashrc
```


测试是否安装配置成功：

```shell
   $ java -version     
```


准备工作一定要做好，否则后面的无法进行

# 单机模式安装

首先，需要把下载好的压缩包解压：

```shell
    $ sudo tar -zxvf ~/下载/hadoop-2.7.3.tar.gz -C /usr/local
    $ cd /usr/local
    $ sudo mv ./hadoop-2.7.3 ./hadoop
    $ sudo chown -R hadoop ./hadoop
```

检测是否可用：

```shell
    $ cd /usr/local/hadoop
    $ ./bin/hadoop version
```

说明此时 `Hadoop` 已经可以用了，这样单机模式无需配置就能用了。但是单机模式只能用来测试，想要真正模拟一个集群，我们至少需要伪分布模式

# 伪分布模式安装

这里需要修改两个文件，位于 `/usr/local/hadoop/etc/hadop` 中，一个是核心配置文件 `core-site.xml` ，一个是 `hdfs` 的配置文件 `hdfs-site.xml`

**core-site.xml:**

```xml
    <configuration>
        <property>
            <name>hadoop.tmp.dir</name>
            <value>file:/usr/local/hadoop/tmp</value>
            <description>Abase for other temporary directories.</description>
        </property>
        <property>
            <name>fs.defaultFS</name>
            <value>hdfs://localhost:9000</value>
        </property>
    </configuration>
```

**hdfs-site.xml**

```xml
    <configuration>
        <property>
            <name>dfs.replication</name>
            <value>1</value>
        </property>
        <property>
            <name>dfs.namenode.name.dir</name>
            <value>file:/usr/local/hadoop/tmp/dfs/name</value>
        </property>
        <property>
            <name>dfs.datanode.data.dir</name>
            <value>file:/usr/local/hadoop/tmp/dfs/data</value>
        </property>
    </configuration>
```

配置完成后，执行 `NameNode` 的格式化：

```shell
    $ ./bin/hdfs namenode -format
```

接着开启 `NameNode` 和 `DataNode` 的守护进程:

```shell
    $ ./sbin/start-dfs.sh
```

最后，用 `jps` 判断是否启动成功：

```shell
    $ jps
```

这样，一个伪分布式的环境就基本上配置成功了！

虽然麻烦，但是配好之后就可以安心的用啦！

