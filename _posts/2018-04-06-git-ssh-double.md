---
layout: post
title: "Git 配置多个 ssh 账户"
categories: Git
tags: 版本库
---

* content
{:toc}



## 前言

我们知道，当我们需要在公司 git 账号或者私人 git 账号使用 ssh 的方式时，需要做相应的配置。下面简单说一下：

---

## 配置

1. 创建两个密钥：

```sh
    ssh-keygen -t rsa -C "yourmail@gmail.com"
```

创建对应账户的公钥和私钥即可

2. 将私钥添加到 `ssh-agent` 中：

```sh
    ssh-add ~/.ssh/id_rsa_1
    ssh-add ~/.ssh/id_rsa_2
```

3. 在 `～/.ssh` 下创建 `config` 文件，`git ssh` 会自动读取：

```sh
    # gitlab
    Host gitlab.company.com
    HostName gitlab.company.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_1

    # github
    Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_2
```

`Host` 和 `HostName` 一般是服务器域名，`IdentityFile` 指私钥文件

4. 测试连接：

```sh
    ssh -T gitlab.company.com
    ssh -T git@github.com
```



