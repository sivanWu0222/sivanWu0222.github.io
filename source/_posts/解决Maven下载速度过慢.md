---
title: (Maven学习-3)解决Maven下载速度过慢
author: yirufeng
top: false
cover: false
coverImg: /images/1.jpg
toc: false
mathjax: false
categories: maven
keywords: maven
sidebar: [blogger, webinfo, tagcloud, category]
tags:
  - maven
date: 2020-05-21 11:18:42
password:
---



进入到maven的安装目录 

>/usr/local/maven/apache-maven-3.6.3/

修改conf目录下的settings.xml配置文件，mirrors修改为如下内容(使用阿里云的maven镜像)

```xml
 <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://my.repository.com/repo/path</url>
    </mirror>
     -->
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>       
    </mirror>
  </mirrors>
```

