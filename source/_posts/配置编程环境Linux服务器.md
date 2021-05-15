---
title: 配置编程环境Linux服务器
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
  - linux
tags:
  - linux
  - 服务器
  - 开发环境安装
date: 2021-03-21 21:17:42
summary:
---

{% note info, 前提：将所有软件包放到`~`目录下%}


{% folding , 安装Git %}
{% note info, 操作存放在`~`目录下的`Git-v2.26.2.tar.gz`文件 %}

{% folding green, 解压并安装 %}
```shell
tar -zxvf Git-v2.26.2.tar.gz
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc-c++ perl-ExtUtils-MakeMaker
cd git-2.26.2/
yum -y install autoconf
make configure
./configure --prefix=/usr/local/git
make profix=/usr/local/git
make install
yum remove git
```
{% endfolding %}

{% folding green, 之后加入环境变量 %}
1. 编辑配置⽂件：`vim /etc/profile`
2. 尾部加⼊Git的bin路径配置即可：
  - `export GIT_HOME=/usr/local/git` 
  - `export PATH=$PATH:$GIT_HOME/bin`
3. 执行`source /etc/profile`使环境变量⽣效即可。
{% endfolding %}
{% endfolding %}



{% folding , 安装JDK %}
{% note info, 操作存放在`~`目录下的`jdk-8u161-linux-x64.tar.gz` %}

{% folding green, 卸载系统自带的openJDK %}
1. `rpm -qa | grep java` 
2. `yum -y remove 我们上一步执行的结果`
{% endfolding %}


{% folding green, 开始安装我们自己的jdk %}
1. `cd /usr/local/`
2. `mkdir java`
3. `cd java`
4. `tar -zxvf /root/jdk-8u161-linux-x64.tar.gz -C ./`
5. 编辑`/etc/profile`⽂件，在⽂件尾部加⼊如下JDK环境配置即可：
```shell
JAVA_HOME=/usr/local/java/jdk1.8.0_161 
CLASSPATH=$JAVA_HOME/lib/ 
PATH=$PATH:$JAVA_HOME/bin 
export PATH JAVA_HOME CLASSPATH
```
6. `source /etc/profile`
7. 验证安装结果：执行`java -version`与 `javac`
{% endfolding %}
{% endfolding %}


{% folding , 安装node %}
{% note info, 操作存放在`~`目录下的`node-v12.16.3-linux-x64.tar.xz` %}

1. `cd /usr/local/`
2. `mkdir node`
3. `cd node`
4. `tar -xJvf /root/node-v12.16.3-linux-x64.tar.xz -C ./`
5. 编辑`/etc/profile`⽂件，在⽂件尾部加⼊如下环境配置即可：
```shell
# Nodejs 
export PATH=/usr/local/node/node-v12.16.3-linux-x64/bin:$PATH
```
6. `source /etc/profile`
7. 验证安装结果：执行`node -v`与 `npm version`与 `npx -v`都输出了版本信息即可。
{% endfolding %}

<!-- more -->


{% folding , 安装python环境 %}
> 默认自带了python2.7环境，但是我们要安装python3

{% note info, 操作存放在`~`目录下的`Python-3.8.3.tgz` %}

1. 执行`tar zxvf Python-3.8.3.tgz`，则可以在当前⽬录得到⽂件夹：Python-3.8.3
2. 安装相关预备环境：`yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make`
3. 编译并安装：
```shell
cd Python-3.8.3/
./configure prefix=/usr/local/python3
make && make install
```
等安装过程完毕，`/usr/local/python3` ⽬录就会⽣成了
4. 我们还需要将刚刚安装⽣成的⽬录`/usr/local/python3`⾥的python3可执⾏⽂件做⼀份软链接，链接到`/usr/bin`下，⽅便后续使⽤python3
```shell
ln -s /usr/local/python3/bin/python3 /usr/bin/python3 
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```
5. 验证安装：`python3`可以正常交互，同时键入`python`也可以正常交互。![y4y9e2](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/y4y9e2.png)
{% endfolding %}

{% folding , MAVEN项目构建和管理工具安装 %}
{% note info, 操作存放在`~`目录下的`apache-maven-3.6.3-bin.tar.gz` %}

1. 创建目录：`mkdir /opt/maven`
2. 将`apache-maven-3.6.3-bin.tar.gz`拷贝到`/opt/maven`目录下：`cp apache-maven-3.6.3-bin.tar.gz /opt/maven/`
3. 移动到目录：`cd /opt/maven/`
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
7. 刷新环境变量让maven环境的路径配置生效：`source /etc/profile`
8. 检验安装结果：执行`mvn -v`可以打印出maven的版本信息，说明安装并且配置成功。![N7MvsE](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/N7MvsE.png)
{% endfolding %}


{% folding , mysql数据库安装部署 %}
{% note info, 操作存放在`~`目录下的`mysql-5.7.30-linux-glibc2.12-x86_64.tar.gz` %}

1. 卸载系统⾃带的MARIADB（如果有）
  1. `rpm -qa|grep mariadb`
  2. `yum -y remove 上一步的结果`
2. 解压mysql安装包：`tar -zxvf /root/mysql-5.7.30-linux-glibc2.12-x86_64.tar.gz -C /usr/local/`
3. `cd /usr/local`
4. `mv mysql-5.7.30-linux-glibc2.12-x86_64 mysql`
5. 创建MYSQL⽤户和⽤户组：`groupadd mysql` 和 `useradd -g mysql mysql`
6. 修改MYSQL⽬录的归属⽤户：`chown -R mysql:mysql ./`
7. 准备mysql的配置文件：在`/etc`⽬录下新建`my.cnf`⽂件
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
8. `mkdir /var/lib/mysql` 和 `chmod 777 /var/lib/mysql`
9. 正式安装：
  1. `cd /usr/local/mysql`
  2. `yum -y install numactl`
  3. `./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data` 得到结果：![CEPS7u](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/CEPS7u.png)
10. 复制脚本到资源目录：`cp ./support-files/mysql.server /etc/init.d/mysqld`
11. 修改`/etc/init.d/mysqld`，修改其basedir和datadir为实际对应⽬录
```shell
basedir=/usr/local/mysql 
datadir=/usr/local/mysql/data
```
12. 设置mysql系统服务并开启自启
  1. ⾸先增加mysqld服务控制脚本执⾏权限：`chmod +x /etc/init.d/mysqld`
  2. 同时将`mysqld`服务加⼊到系统服务：`chkconfig --add mysqld`
  3. 最后检查mysqld服务是否已经⽣效即可：`chkconfig --list mysqld`。结果如图所示：![56wssx](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/56wssx.png)这样就表明 ⽤ service服务已经⽣效了，在2、3、4、5运⾏级别随系统启动⽽⾃动启动，以后可以直接使 命令控制 mysql 的启停。
13. 启动MysqlD：`service mysqld start`
14. 将MYSQL的BIN⽬录加⼊PATH环境变量：`vim ~/.bash_profile`，然后文件末尾添加如下信息：`export PATH=$PATH:/usr/local/mysql/bin`
15. 执行命令使环境变量生效：`source ~/.bash_profile`
16. 首次登陆mysql：
  1. `mysql -u root -p` 之后键入我们上面保存的密码
  2. 进入mysql命令行：执行如下命令更换密码：⽐如这⾥将密码设置成简单的“111111”了。
    ```
    mysql>alter user user() identified by "111111"; 
    mysql>flush privileges;
    ```
17. 设置主机远程登陆：
```
mysql> use mysql; 
mysql> update user set user.Host='%' where user.User='root'; 
mysql> flush privileges;
```
18. 最后利⽤NAVICAT等⼯具进⾏测试即可：![uFYbgl](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/uFYbgl.png)

注意：如果在最后的navicat工具连接不上自己的虚拟机，请确保防火墙关闭。


{% endfolding %}


{% folding , redis安装 %}
{% note info, 操作存放在`~`目录下的`redis-5.0.8.tar.gz` %}

1. `cd /usr/local/`
2. ` mkdir redis `
3. `cd redis`
4. `tar zxvf /root/redis-5.0.8.tar.gz -C ./`
5. `cd redis-5.0.8/`
6. `make && make install`
7. `cd utils/`
8. `./install_server.sh`：全部选择默认配置即可(直接回车)：![c55LQP](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/c55LQP.png)
9. 查看状态：`systemctl status redis_6379.service` 得到如下图中的结果![dkA27L](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/dkA27L.png)
10. `redis-cli` 
11. `vim /etc/redis/6379.conf`
  - 设置允许远程连接：将`bind 127.0.0.1`修改为`bind 0.0.0.0`
  - 重启redis服务：`systemctl restart redis_6379.service`
  - 设置访问密码：将`#requirepass foobared`修改为`requirepass 你想设置的密码`
  - 重启redis服务：`systemctl restart redis_6379.service`
  - 执行`redis-cli`
12. 之后redis客户端执行命令都需要访问密码，使用`auth 我们设置的redis密码`

{% endfolding %}


{% folding , 安装消息队列rabbitMQ %}
{% note info, 因为 RabbitMQ 需要 erlang环境的⽀持，所以必须先安装`erlang`。我们这⾥要安装的是 `erlang-22.3.3-1.el7.x86_64.rpm`，所以首先执行如下命令来安装其对应的`yum repo` ：  %}

注意：如果之前安装了rabbitMQ以及erlang，所以执行如下命令卸载rabbitMQ和erlang

```shell
yum list | grep erlang
yum -y remove erlang-*
yum remove erlang.x86_64
```
```shell
rabbitmqctl app_stop
yum list | grep rabbitmq
yum -y remove rabbitmq-server.noarch
```

1. `curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash`
2. `yum install erlang-22.3.3-1.el7.x86_64`
3. 执行`erl`命令查看是否安装成功，![bzef2G](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/bzef2G.png)
4. 安装rabbitMQ：
  1. `curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash`
  2. `yum install rabbitmq-server-3.8.3-1.el7.noarch`
5. 设置RABBITMQ开机启动：`chkconfig rabbitmq-server on`
6. 启动RABBITMQ服务：`systemctl start rabbitmq-server.service`
7. 开启WEB可视化管理插件：`rabbitmq-plugins enable rabbitmq_management`
8. 访问可视化管理界⾯：`你的服务器IP:15672`
9. 我们可以在后台先添加⼀个⽤户/密码对：`rabbitmqctl add_user 可视化用户名 可视化用户名对应的密码`和 `rabbitmqctl set_user_tags 可视化用户名 administrator`
10. 登录网页：`你的服务器IP:15672` ![5FKYuj](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/5FKYuj.png)

{% endfolding %}

{% folding , tomcat安装部署 %}
{% note info, 操作存放在`~`目录下的`apache-tomcat-8.5.55.tar.gz` %}

1. `cd /usr/local/`
2. `mkdir tomcat`
3. `cd tomcat`
4. `tar -zxvf /root/apache-tomcat-8.5.55.tar.gz -C ./`
5. `cd apache-tomcat-8.5.55/bin`
6. `./startup.sh` ，访问`主机ip地址:8080`得到下图的网页内容：![gpjAfM](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/gpjAfM.png)
7. `cd /etc/rc.d/init.d/`
8. `touch tomcat`
9. `chmod +x tomcat`，接下来编辑`tomcat`文件并键入如下内容：
```
#!/bin/bash 
#chkconfig:- 20 90 
#description:tomcat 
#processname:tomcat 
TOMCAT_HOME=/usr/local/tomcat/apache-tomcat-8.5.55 
case $1 in
  start) su root $TOMCAT_HOME/bin/startup.sh;; 
  stop) su root $TOMCAT_HOME/bin/shutdown.sh;; 
  *) echo "require start|stop" ;;
esac
```
这样后续对于Tomcat的开启和关闭只需要执⾏如下命令即可：`service tomcat start`和`service tomcat stop`
10. `chkconfig --add tomcat`
11. `chkconfig tomcat on`
{% endfolding %}

{% folding , nginx安装  %}
{% note info, 操作存放在`~`目录下的`nginx-1.17.10.tar.gz` %}

1. `cd /usr/local/` 
2. `mkdir nginx` 
3. `cd nginx` 
4. `tar zxvf /root/nginx-1.17.10.tar.gz -C ./` 
5. `yum -y install pcre-devel` 
6. `yum -y install openssl openssl-devel` 
7. 编译并安装：
  - ` cd nginx-1.17.10/` 
  - `./configure`
  - `make && make install`
  - 安装完成之后，nginx可执行位置位于：`/usr/local/nginx/sbin/nginx`
8. 启动nginx：`/usr/local/nginx/sbin/nginx`
  - 如果想停⽌Nginx服务，可执⾏：`/usr/local/nginx/sbin/nginx -s stop`
  - 如果修改了配置⽂件后想重新加载Nginx，可执⾏：`/usr/local/nginx/sbin/nginx -s reload`
  - 注意其配置⽂件位于：`/usr/local/nginx/conf/nginx.conf`
9. 验证：访问主机ip地址可以得到下图中的网页：![fKZxIX](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/fKZxIX.png)
{% endfolding %}

{% folding , 安装docker %}
1. 安装docker：`yum install -y docker`
2. 开启docker服务：`systemctl start docker.service`
3. 查看安装结果：`docker version`
4. 设置开机启动：`systemctl enable docker.service`
5. 配置DOCKER镜像下载加速：
  - 直接编辑配置⽂件：`vim /etc/docker/daemon.json`
  - 加⼊加速镜像源地址即可：
```
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```
6. 加完加速地址后，重新加载配置⽂件，重启 docker 服务即可：`systemctl daemon-reload` 和 `systemctl restart docker.service`

{% endfolding %}



{% folding , kubernetes集群部署 %}
{% note info, 因为是集群，暂时没有做 %}
{% endfolding %}


{% folding , ELASTICSEARCH集群部署 %}
{% note info, 因为是集群，暂时没有做 %}
{% endfolding %}

{% folding , ZOOKEEPER安装部署 %}
{% note info, 操作存放在`~`目录下的`apache-zookeeper-3.6.1-bin.tar.gz` %}

1. `cd /usr/local/`
2. `mkdir zookeeper`
3. `cd zookeeper/`
4. `tar -zxvf /root/apache-zookeeper-3.6.1-bin.tar.gz -C ./`
5. `cd apache-zookeeper-3.6.1-bin/`
6. `mkdir data`
7. `cd conf/`
8. `cp zoo_sample.cfg zoo.cfg`
9. `sudo vim zoo.cfg`：修改配置⽂件`zoo.cfg`，将其中的dataDir(`/usr/local/zookeeper/apache-zookeeper-3.6.1-bin/data`)修改为上⾯刚创建的data⽬录。
10. `cd ..`
11. `./bin/zkServer.sh start` 
12. `./bin/zkServer.sh status` 显示的结果如下：![CBPjNm](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/CBPjNm.png) **因为我们安装的是3.6.1的版本(在3.5.5版本及以上，Zookeeper 提供了一个内嵌的Jetty容器来运行 AdminServer，默认占用的是 8080端口，AdminServer 主要是来查看 Zookeeper 的一些状态，如果机器上有其他程序（比如：Tomcat）占用了 8080 端口，也会导致 Starting zookeeper … FAILED TO START 的问题。)：如果出现Starting zookeeper ... FAILED TO START，请在我们刚才的zoo.cfg配置文件最后添加配置项，修改后重启即可：**
```
# admin port
admin.serverPort=9000
```
13. `vim /etc/profile` ，尾部加入zookeeper的bin路径配置即可：
```
export ZOOKEEPER_HOME=/usr/local/zookeeper/apache-zookeeper-3.6.1-bin 
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```
14. `source /etc/profile`
15. 进入目录：`cd /etc/rc.d/init.d`，创建文件，并赋予执行权限，`touch zookeeper`与`chmod +x zookeeper`
16. 编辑zookeeper文件`vim zookeeper`：
```
#!/bin/bash 
#chkconfig: - 20 90 
#description: zookeeper 
#processname: zookeeper 
ZOOKEEPER_HOME=/usr/local/zookeeper/apache-zookeeper-3.6.1-bin 
export JAVA_HOME=/usr/local/java/jdk1.8.0_161 # 此处根据你的实际情况更换对 应 
case $1 in
  start) su root $ZOOKEEPER_HOME/bin/zkServer.sh start;; stop) su root $ZOOKEEPER_HOME/bin/zkServer.sh stop;; status) su root $ZOOKEEPER_HOME/bin/zkServer.sh status;; restart) su root $ZOOKEEPER_HOME/bin/zkServer.sh restart;; *) echo "require start|stop|status|restart" ;;
esac
``` 
注意：如果出现`service zookeeper does not support chkconfig`说明我们这个zookeeper文件没写对，重新检查是否和上面的内容一致。
17. 最后加入开启启动即可：`chkconfig --add zookeeper` 和 `chkconfig zookeeper on`
{% endfolding %}


{% folding , 消息队列kafka安装部署 %}
> 因为Kafka依赖zookeeper，所以得先安装zookeeper

{% note info, 操作存放在`~`目录下的`kafka_2.12-2.5.0.tgz` %}

1. `cd /usr/local/`
2. `mkdir kafka`
3. `cd kafka`
4. `tar -zxvf /root/kafka_2.12-2.5.0.tgz -C ./`
5. `cd /usr/local/kafka/kafka_2.12-2.5.0`
6. `mkdir logs`
7. `cd config/`
8. 修改配置⽂件`server.properties`，⼀是将其中的`log.dirs`修改为上⾯刚创建的logs⽬录，其他选项可以按需配置`vim server.properties`
9. 另外关注⼀下连接ZooKeeper的相关配置，根据实际情况进⾏配置：![anf5Xj](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/anf5Xj.png)
10. 启动kafka：在kafka根目录下执行`./bin/kafka-server-start.sh ./config/server.properties` 或者如果需要后台启动，则加上`-daemon`参数即可，也就是执行命令：`./bin/kafka-server-start.sh -daemon ./config/server.properties`
11. ⾸先创建⼀个名为codesheep的topic：`./bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic codesheep` ![u4xtBh](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/u4xtBh.png)
  - 创建完成以后，可以使⽤命令来列出⽬前已有的topic列表
  - 接下来创建⼀个⽣产者，⽤于在codesheep这个topic上⽣产消息：`./bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic codesheep`
  - ⽽后**在另外一个终端**创建⼀个消费者，⽤于在codesheep这个topic上获取消息：`./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic codesheep`
  - 此时⽣产者发出的消息，在消费者端可以获取到：![TnUlHU](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/TnUlHU.png)

{% endfolding %}



