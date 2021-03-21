---
title: 配置编程环境Linux服务器
author: yirufeng
pin: true
toc: true
mathjax: false
sidebar:
  - blogger
  - webinfo
  - tagcloud
  - category
categories:
  - linux
tags:
  - linux
  - 服务器
  - 开发环境安装
date: 2021-03-21 21:17:42
summary:
---


前提：将所有软件包放到`~`目录下

## 安装git
操作存放在`~`目录下的`Git-v2.26.2.tar.gz`文件

1. {% note info, `tar -zxvf Git-v2.26.2.tar.gz` %}
2. {% note info, `yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc-c++ perl-ExtUtils-MakeMaker` %}
3. {% note info, `cd git-2.26.2/` %}
4. {% note info, `yum -y install autoconf` %}
5. {% note info, `make configure` %}
6. {% note info, `./configure --prefix=/usr/local/git` %}
7. {% note info, `make profix=/usr/local/git` %}
8. {% note info, `make install` %}
9. {% note info, `yum remove git` %}


之后加入环境变量：
1. 编辑配置⽂件：{% note info, `vim /etc/profile` %}
2. 尾部加⼊Git的bin路径配置即可：
  - {% note info, `export GIT_HOME=/usr/local/git` %}
  - {% note info, `export PATH=$PATH:$GIT_HOME/bin` %}
3. 执行{% note info, `source /etc/profile` %}使环境变量⽣效即可。


## 安装JDK
操作存放在`~`目录下的`jdk-8u161-linux-x64.tar.gz`

### 卸载系统自带的openJDK
1. {% note info, `rpm -qa | grep java` %}
2. {% note info, `yum -y remove 我们上一步执行的结果` %}



### 开始安装我们自己的jdk

1. {% note info, `cd /usr/local/` %}
2. {% note info, `mkdir java` %}
3. {% note info, `cd java` %}
4. {% note info, `tar -zxvf /root/jdk-8u161-linux-x64.tar.gz -C ./` %}
5. 编辑`/etc/profile`⽂件，在⽂件尾部加⼊如下JDK环境配置即可：
```shell
JAVA_HOME=/usr/local/java/jdk1.8.0_161 
CLASSPATH=$JAVA_HOME/lib/ 
PATH=$PATH:$JAVA_HOME/bin 
export PATH JAVA_HOME CLASSPATH
```
6. {% note info, `source /etc/profile` %}
7. 验证安装结果：执行{% note info, `java -version` %} 与 {% note info, `javac` %}

## 安装node
操作存放在`~`目录下的`node-v12.16.3-linux-x64.tar.xz`
1. {% note info, `cd /usr/local/` %}
2. {% note info, `mkdir node` %}
3. {% note info, `cd node` %}
4. {% note info, `tar -xJvf /root/node-v12.16.3-linux-x64.tar.xz -C ./` %}
5. 编辑`/etc/profile`⽂件，在⽂件尾部加⼊如下环境配置即可：
```shell
# Nodejs 
export PATH=/usr/local/node/node-v12.16.3-linux-x64/bin:$PATH
```
6. {% note info, `source /etc/profile` %}
7. 验证安装结果：执行{% note info, `node -v` %} 与 {% note info, `npm version` %} 与 {% note info, `npx -v` %} 都输出了版本信息即可。

<!-- more -->

## 安装python环境

> 默认自带了python2.7环境，但是我们要安装python3

操作存放在`~`目录下的`Python-3.8.3.tgz`

1. 执行{% note info, `tar zxvf Python-3.8.3.tgz` %}，则可以在当前⽬录得到⽂件夹：Python-3.8.3
2. 安装相关预备环境：{% note info, `yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make` %}
3. 编译并安装：等安装过程完毕，`/usr/local/python3` ⽬录就会⽣成了
```shell
cd Python-3.8.3/
./configure prefix=/usr/local/python3
make && make install
```
4. 我们还需要将刚刚安装⽣成的⽬录`/usr/local/python3`⾥的python3可执⾏⽂件做⼀份软链接，链接到`/usr/bin`下，⽅便后续使⽤python3
```shell
ln -s /usr/local/python3/bin/python3 /usr/bin/python3 
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```
5. 验证安装：`python3`可以正常交互，同时键入`python`也可以正常交互。![y4y9e2](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/y4y9e2.png)


## MAVEN项目构建和管理工具安装
操作存放在`~`目录下的`apache-maven-3.6.3-bin.tar.gz`

1. 创建目录：{% note info, `mkdir /opt/maven` %}
2. 将`apache-maven-3.6.3-bin.tar.gz`拷贝到`/opt/maven`目录下：{% note info, `cp apache-maven-3.6.3-bin.tar.gz /opt/maven/` %}
3. 移动到目录：{% note info, ` cd /opt/maven/` %}
4. 执行命令解压：`tar zxvf apache-maven-3.6.3-bin.tar.gz`
5. 配置maven加速镜像源：
  1. 修改`/opt/maven/apache-maven-3.6.3/conf/settings.xml`文件，并在`<mirrors></mirrors>`标签对里面添加如下内容：![dbSdoB](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/dbSdoB.png)
  ```
  <mirror>
    <id>alimaven</id> 
    <name>aliyun maven</name> 
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
    <mirrorOf>central</mirrorOf> 
  </mirror>
  ```
6. 配置环境变量：编辑`/etc/profile`⽂件，在⽂件尾部加⼊如下环境配置即可：
```shell
export MAVEN_HOME=/opt/maven/apache-maven-3.6.3 
export PATH=$MAVEN_HOME/bin:$PATH
```
7. 刷新环境变量让maven环境的路径配置生效：{% note info, `source /etc/profile` %}
8. 检验安装结果：执行{% note info, `mvn -v` %}可以打印出maven的版本信息，说明安装并且配置成功。![N7MvsE](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/N7MvsE.png)

## mysql数据库安装部署

操作存放在`~`目录下的`mysql-5.7.30-linux-glibc2.12-x86_64.tar.gz`

1. 卸载系统⾃带的MARIADB（如果有）
  1. {% note info, `rpm -qa|grep mariadb` %}
  2. {% note info, `yum -y remove 上一步的结果` %}
2. 解压mysql安装包：{% note info, `tar -zxvf /root/mysql-5.7.30-linux-glibc2.12-x86_64.tar.gz -C /usr/local/` %}
3. {% note info, `cd /usr/local` %}
4. {% note info, `mv mysql-5.7.30-linux-glibc2.12-x86_64 mysql` %}
5. 创建MYSQL⽤户和⽤户组：{% note info, `groupadd mysql` %} 和 {% note info, `useradd -g mysql mysql` %}
6. 修改MYSQL⽬录的归属⽤户：{% note info, `chown -R mysql:mysql ./` %}
7. 准备mysql的配置文件：
```
[mysql] 
# 设置mysql客户端默认字符集 
default-character-set=utf8 
socket=/var/lib/mysql/mysql.sock

[mysqld] 
skip-name-resolve 
#设置3306端⼝ 
port = 3306 
socket=/var/lib/mysql/mysql.sock 
# 设置mysql的安装⽬录 
basedir=/usr/local/mysql 
# 设置mysql数据库的数据的存放⽬录 
datadir=/usr/local/mysql/data 
# 允许最⼤连接数 
max_connections=200 
# 服务端使⽤的字符集默认为8⽐特编码的latin1字符集 character-set-server=utf8 
# 创建新表时将使⽤的默认存储引擎 
default-storage-engine=INNODB

lower_case_table_names=1 
max_allowed_packet=16M
```
8. {% note info, `mkdir /var/lib/mysql` %} 和 {% note info, `chmod 777 /var/lib/mysql` %}
9. 正式安装：
  1. {% note info, `cd /usr/local/mysql` %}
  2. {% note info, `yum -y install numactl` %}
  3. {% note info, `./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data` %} 得到结果：![CEPS7u](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/CEPS7u.png)
10. 复制脚本到资源目录：{% note info, `cp ./support-files/mysql.server /etc/init.d/mysqld` %}
11. 修改`/etc/init.d/mysqld`，修改其basedir和datadir为实际对应⽬录
```shell
basedir=/usr/local/mysql 
datadir=/usr/local/mysql/data
```
12. 设置mysql系统服务并开启自启
  1. ⾸先增加mysqld服务控制脚本执⾏权限：{% note info, `chmod +x /etc/init.d/mysqld` %}
  2. 同时将`mysqld`服务加⼊到系统服务：{% note info, `chkconfig --add mysqld` %}
  3. 最后检查mysqld服务是否已经⽣效即可：{% note info, `chkconfig --list mysqld` %}。结果如图所示：![56wssx](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/56wssx.png)这样就表明 ⽤ service服务已经⽣效了，在2、3、4、5运⾏级别随系统启动⽽⾃动启动，以后可以直接使 命令控制 mysql 的启停。
13. 启动MysqlD：{% note info, `service mysqld start` %}
14. 将MYSQL的BIN⽬录加⼊PATH环境变量：{% note info, `vim ~/.bash_profile` %}，然后文件末尾添加如下信息：`export PATH=$PATH:/usr/local/mysql/bin`
15. 执行命令使环境变量生效：{% note info, `source ~/.bash_profile` %}
16. 首次登陆mysql：
  1. {% note info, `mysql -u root -p` %} 之后键入我们上面保存的密码
  2. 进入mysql命令行：执行如下命令更换密码：⽐如这⾥将密码设置成简单的“111111”了。
    ```
    mysql>alter user user() identified by "111111"; mysql>flush privileges;
    ```
17. 设置主机远程登陆：
```
mysql> use mysql; 
mysql> update user set user.Host='%' where user.User='root'; mysql> flush privileges;
```
18. 最后利⽤NAVICAT等⼯具进⾏测试即可：![uFYbgl](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/uFYbgl.png)

## redis安装

{% note info, `` %}
{% note info, `` %}
{% note info, `` %}

{% noteblock,  %}


{% endnoteblock %}


{% note info,  %}

{% noteblock, 标题（可选） %}

Windows 10不是為所有人設計,而是為每個人設計

{% endnoteblock %}

{% folding green, 查看代码测试 %}

{% endfolding %}