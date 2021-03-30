---
title: 基于Centos7 搭建 gitlab
author: yirufeng
pin: false
toc: true
mathjax: false
sidebar:
  - blogger
  - webinfo
  - tagcloud
  - category
categories:
  - CI/CD
tags:
  - gitlab
  - Tools
  - Linux
date: 2021-03-26 10:37:13
summary:
---

## 环境
centos7腾讯云服务器一台

## 步骤
{% folding green, 前提 %}
1. 更新软件包：`yum update -y`
2. 安装 sshd：`yum install -y curl policycoreutils-python openssh-server`
3. 启用并启动sshd：`systemctl enable sshd` 和 `systemctl start sshd`
4. 配置防火墙：
  1. 打开 `/etc/sysctl.conf` 文件，在文件最后添加新的一行并按 Ctrl + S 保存：`net.ipv4.ip_forward = 1`
  2. 启用并启动防火墙：`systemctl enable firewalld` 和 `systemctl start firewalld`
  3. 放通 HTTP：`firewall-cmd --permanent --add-service=http`
  4. 重启防火墙：`systemctl reload firewalld`
  5. 在实际使用中，可以使用 `systemctl status firewalld` 命令查看防火墙的状态。
5. 安装postfix
  1. GitLab 需要使用 postfix 来发送邮件。当然，也可以使用 SMTP 服务器，具体步骤请参考 [官方教程](https://docs.gitlab.com/omnibus/settings/smtp.html)。
  2. 安装：`yum install -y postfix`
  3. 打开 `/etc/postfix/main.cf` 文件，在第 119 行附近找到 `inet_protocols = all`，将 `all` 改为 `ipv4` 并按 `Ctrl + S` 保存：`inet_protocols = ipv4`
  4. 启用并启动 postfix：`systemctl enable postfix` + `systemctl start postfix`
{% endfolding %}

{% folding green, 配置 swap 交换分区 %}
> 由于 GitLab 较为消耗资源，我们需要先创建交换分区，以降低物理内存的压力。在实际生产环境中，如果服务器配置够高，则不必配置交换分区。
1. 新建 2 GB 大小的交换分区：`dd if=/dev/zero of=/root/swapfile bs=1M count=2048`
2. 格式化为交换分区文件并启用：`mkswap /root/swapfile` 和 `swapon /root/swapfile`
3. 添加自启用。打开 `/etc/fstab` 文件，在文件最后添加新的一行并按 `Ctrl + S` 保存：`/root/swapfile swap swap defaults 0 0`
4. 将软件源修改为国内源：由于网络环境的原因，将 repo 源修改为清华大学。在 `/etc/yum.repos.d` 目录下新建 `gitlab-ce.repo`文件并保存。内容如下：
```
[gitlab-ce]
name=Gitlab CE Repository
baseurl=https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el$releasever/
gpgcheck=0
enabled=1
```
{% endfolding %}

{% folding green, 安装gitlab %}
1. 刚才修改过了 yum 源，因此先重新生成缓存：`yum makecache` {% note info, （此步骤执行时间较长，一般需要 3~5 分钟左右，请耐心等待）%}
2. 安装 GitLab：：`yum install -y gitlab-ce` {% note info, （此步骤执行时间较长，一般需要 3~5 分钟左右，请耐心等待）%}
3. 配置 GitLab 的域名（非必需）：打开 `/etc/gitlab/gitlab.rb` 文件，在第 13 行附近找到 `external_url 'http://gitlab.example.com'`，将单引号中的内容改为自己的域名（带上协议头，末尾无斜杠），并按 Ctrl + S 保存。例如：`external_url 'http://work.myteam.com'` 记得将域名通过 A 记录解析到 centos7对应的IP 哦。
4. 设置完成后使用如下命令重启 GitLab的配置：`sudo gitlab-ctl reconfigure` + 重启gitlab服务`sudo gitlab-ctl restart`{% note info, （此步骤执行时间较长，一般需要 5~10 分钟左右，请耐心等待）%}
5. 至此，我们已经成功地在 CentOS 7 上搭建了 GitLab。 现在可以在这里（http://centoss7主机ip地址/）访问 GitLab 了。
{% endfolding %}


{% folding green, 注意事项 %}
1. 在实际生产中，建议您使用 2 核 4 GB 或更高配置的 CVM。[点击这里](https://docs.gitlab.com/ce/install/requirements.html#cpu) 可以查看 GitLab 官方推荐的配置和可承载人数对应表。
2. 再次提醒您，定期执行 `yum update -y` 以保持各软件包的最新状态。
{% endfolding %}
<!-- more -->



