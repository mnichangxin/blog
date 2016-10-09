---
layout: post
title: "MongoDB Windows下的配置"
categories: MongoDB
tags: MongoDB Sql
---

* content
{:toc}

`Linux` 下配置没什么问题，就是 `Windows` 下有点坑：

## MongoDB的启动

![](http://7xr2ek.com1.z0.glb.clouddn.com/blog/image/mongo-comp.png)




这里主要用到这两个模块：

* `./bin/mongod` 是服务器模块

* `./bin/mongo` 是客户端连接模块

先启动 `MogonDB` 服务，务必在 **管理员命令模式** 下的 `CMD` 下进入 `./bin`

```ruby
    $ mongod --dbpath d:\test\mongodb\data\db
```

如果路径有空格，一定加上引号，如：

```ruby
    $ mongod --dbpath "d:\test\mongo db\data\db"
```

这样就可以启动一个 `MongoDB` 服务，这个窗口先不用关闭

---

## 连接MongoDB服务器

这次用的是 `mongo` 命令

```ruby
    $ mongo
```

可以看到默认 `test` 连接已经成功

---

## Windows服务项配置

上面两步显得有点麻烦，像 `MySql` 开机自动启动，是因为加入了系统启动项，我们也为 `MongoDB` 设置一个启动项，便于启动和停止服务：

```ruby
    $ mongod --logpath "E:\Work Files\MongoDB\data\log\mongo.log" --logappend --dbpath "E:\Work Files\MongoDB\data\db" --directoryperdb --serviceName MongoDB --install
```

`--logpath` 配置的是日志文件路径，`dbpath` 配置的是数据文件路径，`--serviceName` 配置的是系统启动项。这次可以在系统的服务中看到 `MongoDB` 这个服务。

另外，可以通过如下命令启动、停止、删除服务：

```ruby
    $ net start MongoDB #start mongodb
    $ net stop MongoDB #stop mongodb
    $ net delete MongoDB #delete mongodb service
```

---

## 参考资料

[MongoDB官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)
