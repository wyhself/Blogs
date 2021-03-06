---
title: gitlab-ce试用
date: 2019-12-31 17:24:17
tags:
---

## 环境

* OS: Debian 9
* Mem: 4GB
* CPU: 1Core
* Package: Gitlab-ce Omnibus Package

## 安装

1. 安装依赖包

   ```bash
   Debian$ sudo apt-get install -y curl openssh-server ca-certificates
   ```

   接下来安装postfix用来发送通知邮件。如果使用其它邮件服务，可以跳过此步骤。

   ```bash
   Debian$ sudo apt-get install -y postfix
   ```

   安装过程会弹出配置对话框，选择"Internet Site"后回车，其它选择默认项即可，一路回车。

2. 添加gitlab源并安装gitlab-ce
   新增gitlab安装包的源

   ```bash
   Debian$ curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
   ```

   接下来安装gitlab包，其中`EXTERNAL_URL`是gitlab访问地址，引处我们我们使用localhost。

   ```bash
   Debian$ sudo EXTERNAL_URL="http://localhost:9876" apt-get install gitlab-ce
   ```

3. 试用
   安装完成后，我们访问`http://localhost:9876`使用gitlab，初次访问会自动引导`root`密码设置。

## 初识gitlab

1. gitlab运行之后，可通过如下命令查看启用了哪些相关服务

```bash
Debain$ ps aux | grep ^git
```

2. 可通过如下命令查看相关用户的信息，如Home目录

```bash
Debian$ cat /etc/passwd | grep ^git
```

3. 可通过如下命令查看相关安装目录

```bash
Debian$ find / -type d -name "gitlab"
```

4. 目录简介

* `/var/opt/gitlab`是Omnibus package的默认安装位置，也即`git`用户的家目录，它是应用数据和配置的存放位置。
* `/etc/gitlab`是配置文件存放位置，主配置文件为`/etc/gitlab/gitlab.rb`，该目录下文件修改后，通过如下两条命令使配置写回/var/opt/gitlab的相应配置并生效，

```bash
Debian$ gitlab-ctl reconfigure
Debian$ gitlab-ctl restart # 使所有服务stop后start
```

* `/opt/gitlab`是gitlab代码和依赖的存放目录
* `/var/log/gitlab`是gitlab及组件的日志目录

## gitlab运维相关

### 配置文件

主配置文件为*/etc/gitlab/gitlab.rb*，该文件几乎包含了gitlab所有服务的配置，配置文件中有详细的配置说明，或文档链接，下面列举一些常见配置项

```bash
# 访问地址
external_url 'http://localhost:9876'

# worker数
unicorn['worker_processes'] = 2
# 建议为CPU核心数 + 1，不能低于2

# 数据库并发数
postgresql['max_worker_processes'] = 8
# 数据库缓存
postgresql['shared_buffers'] = "256MB"

# sidekiq并发数
sidekiq['concurrency'] = 25

```

### 运维命令

```bash
$ gitlab-ctl help
$ gitlab-ctl status
$ gitlab-ctl show-config
$ gitlab-ctl service-list
$ gitlab-ctl tail
$ gitlab-ctl reconfigure
$ gitlab-ctl stop
$ gitlab-ctl restart
```

## 参考

* [gitlab官方文档]("https://about.gitlab.com/install/?version=ce#debian" "giblab安装文档")
* [gitlab安装要求]("https://docs.gitlab.com/ce/install/requirements.html" "gitlab install requirements")

