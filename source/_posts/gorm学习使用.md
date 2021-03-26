---
title: gorm学习使用
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
  - go
tags:
  - 框架
  - orm
date: 2021-03-24 21:55:58
summary:
---
# gorm的使用

## 安装
1. 使用指令进行安装：`go get github.com/jinzhu/gorm`

## DDL操作

### 前提
1. 新建一个数据库gorm_project
2. 指定使用Utf8编码

### 数据库连接

{% folding green, 对应的代码 %}

```go
package main

import (
   "fmt"
   "github.com/jinzhu/gorm"
   //_ "github.com/go-sql-driver/mysql" //导入驱动方式一
   _ "github.com/jinzhu/gorm/dialects/mysql" //导入驱动方式二，前面加上_表示不去校验用没用
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/22 10:00 下午
 * @Desc:
 **/

var (
   MYSQL_USERNAME = "root"
   MYSQL_PASSWORD = "qwertyasdfgh"
   MYSQL_IP       = "81.69.26.201"
   MYSQL_PORT     = "3306"
   MYSQL_DATABASE = "gorm_project"
)

func main() {

   //parseTime用来处理time.Time类型的数据
   //loc=Local表示根据本地时区走
   //连接信息：用户名:密码@tcp(ip:port)/数据库名称?charset=utf8&parseTime=True&loc=Local
   connInfo := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8&parseTime=True&loc=Local",
      MYSQL_USERNAME, MYSQL_PASSWORD, MYSQL_IP, MYSQL_PORT, MYSQL_DATABASE)

   //使用open来连接数据库
   //第一个参数是dialect也就是指定数据库的类型
   //第二个参数是连接信息
   db, err := gorm.Open("mysql", connInfo)
   if err != nil {
      panic(err)
   }

   defer db.Close() //关闭空闲的连接
}
```

{% endfolding %}

### 数据库对表的操作

拆分出的一个包："gorm_demo/conn"
{% folding green, 对应的代码 %}

```go
package conn

import (
   "fmt"
   "github.com/jinzhu/gorm"
   //_ "github.com/go-sql-driver/mysql" //导入驱动方式一
   _ "github.com/jinzhu/gorm/dialects/mysql" //导入驱动方式二，前面加上_表示不去校验用没用

)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 9:06 上午
 * @Desc:
 **/


var (
   MYSQL_USERNAME = "root"
   MYSQL_PASSWORD = "qwertyasdfgh"
   MYSQL_IP       = "81.69.26.201"
   MYSQL_PORT     = "3306"
   MYSQL_DATABASE = "gorm_project"

   db *gorm.DB
)

func init() {
   //parseTime用来处理time.Time类型的数据
   //loc=Local表示根据本地时区走
   //连接信息：用户名:密码@tcp(ip:port)/数据库名称?charset=utf8&parseTime=True&loc=Local
   connInfo := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8&parseTime=True&loc=Local",
      MYSQL_USERNAME, MYSQL_PASSWORD, MYSQL_IP, MYSQL_PORT, MYSQL_DATABASE)

   //使用open来连接数据库
   //第一个参数是dialect也就是指定数据库的类型
   //第二个参数是连接信息
   var err error
   db, err = gorm.Open("mysql", connInfo)
   if err != nil {
      panic(err)
   }
}


func GetConn() *gorm.DB {
   return db
}


func Close() {
   db.Close()
}
```
{% endfolding %}

#### 创建表

{% folding green, 对应的代码 %}

```go
package main

import (
   "fmt"
   "gorm_demo/conn"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 9:03 上午
 * @Desc:
   连接数据库并对数据库中的表进行操作：
   1. 创建表
   2. 删除表
 **/

//创建表之前先得有一个模型
//字段中记得习惯性将首字母大写
type User struct {
   Id      int
   Name    string
   Age     int
   Address string
   Pic     string
}

//不指定表名直接根据我们传入的结构体创建一个表
/*
生成的表的结果如下：表名是users
字段名分别是id,name,age,addr,pic
*/
func demo1() {
   //创建表的
   conn.GetConn().CreateTable(&User{})

   //释放连接
   conn.Close()
}

//根据指定的表名创建表
func demo2() {
   //创建表
   conn.GetConn().Table("user").CreateTable(&User{})

   //释放连接
   conn.Close()
}

func main() {
   //demo1()
   //demo2()
   demo3()
}
```
{% endfolding %}


#### 删除表
{% folding green, 对应的代码 %}

```go
package main

import (
   "fmt"
   "gorm_demo/conn"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 9:03 上午
 * @Desc:
   连接数据库并对数据库中的表进行操作：
   1. 创建表
   2. 删除表
 **/

//创建表之前先得有一个模型
//字段中记得习惯性将首字母大写
type User struct {
   Id      int
   Name    string
   Age     int
   Address string
   Pic     string
}

//删除表
func demo3() {
   db := conn.GetConn()
   //删除表的方式一：使用我们模型名
   db.DropTable(&User{})  //删除的就是users，因为删除的表的名字是模型名通过规则变成的表名

   //删除表的方式二：直接使用表名
   db.DropTable("user")

   //删除表的方式三：先判断是否存在之后再进行删除
   db.DropTableIfExists(&User{})

   //释放连接
   db.Close()
}

func main() {
   //demo1()
   //demo2()
   demo3()
}
```
{% endfolding %}


#### 查询表是否存在
{% folding green, 对应的代码 %}

```go
package main

import (
   "fmt"
   "gorm_demo/conn"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 9:03 上午
 * @Desc:
   连接数据库并对数据库中的表进行操作：
   1. 创建表
   2. 删除表
 **/

//创建表之前先得有一个模型
//字段中记得习惯性将首字母大写
type User struct {
   Id      int
   Name    string
   Age     int
   Address string
   Pic     string
}

//检查表是否存在
func demo4() {
   db := conn.GetConn()

   //方式一：使用模型
   b1 := db.HasTable(&User{})
   fmt.Println(b1)
   //方式二：使用指定的表名
   b2 := db.HasTable("users")
   fmt.Println(b2)

}

func main() {
   //demo1()
   //demo2()
   demo3()
}
```
{% endfolding %}

### [不讲，也不建议使用]对数据表中列的操作
> 一般很少有人修改，比如我们定义好的一个字段的类型，如果再去修改将会给别人造成一个很大的坑的感觉
1. ![Kvp99B](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/Kvp99B.png)
2. ![KcxqMH](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/KcxqMH.png)


### 数据库的迁移

自动迁移会创建表，帮助我们添加缺少的列和索引，不会改变现有列的类型以及不会删除未使用的列来保护数据
{% folding green, 对应的代码 %}


```go
//方式一：迁移一个表
db.AutoMigrate(&User{})

//方式二：迁移多个表
db.AutoMigrate(&User{},&Product{},&Order{})

//方式三：创建表的时候添加表后缀
db.Set("gorm:table_options", "ENGINE=InnoDB").AutoMigrate(&Student{})
```
{% endfolding %}

## DML操作

> 主要是对数据的操作

### 增加数据
{% folding green, 对应的代码 %}


```go
package main

import (
   "gorm_demo/conn"
   "gorm_demo/models"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 10:43 上午
 * @Desc:
 **/

func main() {
   db := conn.GetConn()
   defer db.Close()

   //自动迁移数据表
   db.AutoMigrate(&models.User{})


   //新增数据
   db.Create(&models.User{
      Name: "张三",
      Age: 18,
      Addr: "XXX",
      Pic: "/static/upic/download.png",
      Phone: "13967845581",
   })

}

```
{% endfolding %}
### 查询数据
{% folding green, 对应的代码 %}


```go
package main

import (
   "fmt"
   "gorm_demo/conn"
   "gorm_demo/models"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 10:47 上午
 * @Desc:
   使用first查询第一条数据，两个方式：
      1. 根据我们默认的条件进行查询，默认是主键的值
      2. 根据我们自己指定的条件进行查询
 **/

func main() {
   db := conn.GetConn()
   defer db.Close()

   var user models.User

   //查询第一条数据
   //第一个参数是容器，表示将查询结果放到哪里，
   //第二个参数是条件，默认是id
   db.First(&user, 1) //这里的1默认是id

   var user2 models.User

   //根据指定的条件进行查询
   db.First(&user2, "name=?", "张三")
   fmt.Println(user)
   fmt.Println(user2)
}

```
{% endfolding %}
### 修改数据
{% folding green, 对应的代码 %}
```go
package main

import (
   "gorm_demo/conn"
   "gorm_demo/models"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 10:53 上午
 * @Desc:

   更新：先查询出对应的数据，然后
 **/

func main() {
   db := conn.GetConn()
   defer db.Close()
   var user models.User

   //查询第一条数据
   //第一个参数是容器，表示将查询结果放到哪里，
   //第二个参数是条件，默认是id
   db.First(&user, 1) //这里的1默认是id


   //更新一个字段
   db.Model(&user).Update("Age", 30)

   //更新多个字段，【推荐】链式调用或者一行代码更新一个
   db.Model(&user).Update("Age", 35).Update("name", "yirufeng")

}
```
{% endfolding %}
### 删除数据

{% folding green, 对应的代码 %}


```go
package main

import (
   "fmt"
   "gorm_demo/conn"
   "gorm_demo/models"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 11:03 上午
 * @Desc: 删除数据
 **/



func main() {
   db := conn.GetConn()
   defer db.Close()

   //新增数据
   db.Create(&models.User{
      Name: "张三",
      Age: 18,
      Addr: "XXX",
      Pic: "/static/upic/download.png",
      Phone: "13967845581",
   })

   //删除之前先要查询数据
   var user models.User
   db.First(&user, 3)
   fmt.Println(user)
   //删除数据
   db.Delete(&user)
}
```
{% endfolding %}


## 【模型定义】模型名和表名的映射关系

**模型定义作用：用于数据库数据转换以及自动建表**

规则：

   - 第一个大写字母变为小写
   - 遇到其他大写字母变为小写并在前面加上下划线 **比如`UserInfo`变成`user_infos**`
   - 连着的几个大写字母，只有第一个遵循上面的两条规则，其他的大写字母转换为小写，不加下划线，遇到小写，前面的第一个大写字母变小写并在该大写字母前面加下划线。比如`DBUserInfo`变成`db_user_infos`
   - 复数形式

举例：

1. User —> users 首字母小写，复数
2. UserInfo —> user_infos
3. DBUserInfo —> db_user_infos
4. DBXXXXUserInfo —> dbxxxx_user_infos

## 【模型定义】自定义表名称

模型名如何与表名进行自定义名称呢：也就是我们模型名定义之后，但是想定义在数据库的表名，我们可以按照如下思路做：在我们的结构体对应的.go文件中编写一个方法`func (struct名称) TableName() string`
{% folding green, 对应的代码 %}


```go
package main

import "gorm_demo/conn"

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 2:51 下午
 * @Desc:
 **/

type StudentInfo struct {
   Id   int
   Name string
   Age  int
}

//对应的struct绑定一个方法
func (StudentInfo) TableName () string {
   return "students"
}

func main() {
   db := conn.GetConn()
   defer db.Close()
   db.AutoMigrate(&StudentInfo{})
}
```
{% endfolding %}
**给默认生成的表名前面加上前缀和后缀**：给默认表名上面同一加上其他规则，只能在我们生成的默认表名的前面或后面加上指定的内容，不可以在中间加上指定的内容。
{% folding green, 对应的代码 %}


```go
package main

import (
   "github.com/jinzhu/gorm"
   "gorm_demo/conn"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 3:19 下午
 * @Desc:
 **/


type Employ struct {
   Id   int
   Name string
   Age  int
}


func main() {
   db := conn.GetConn()
   defer db.Close()

   //给我们生成的默认表名的前面加上"sys_"以及后面加上"_sys"
   gorm.DefaultTableNameHandler = func(db *gorm.DB, defaultTableName string) string {
      return "sys_" + defaultTableName + "_sys"
   }

   db.AutoMigrate(&Employ{})
}
```
{% endfolding %}
结果：在数据库中生成的表名是`sys_employs_sys`

## 【模型定义】字段名称和列名的映射关系

规则：列名是字段名的蛇形小写

举例：Name—>name ，CreatedTime —> created_time DBACreateTime —>dba_create_time

可以通过gorm标签指定列名：**AnimalId int64 `gorm:"column:animal_id"`**

## 【模型定义】gorm.Model

![WStdsY](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/WStdsY.png)

## 【模型定义】gorm标签的使用

gorm中的primary_key默认就是auto_increment的

![Gq5XHn](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/Gq5XHn.png)

**结构体标签gorm的使用**
{% folding green, 对应的代码 %}


```go
type UserInfo struct {
  Id int `gorm:"primary_key"`
  Name string `gorm:"index"`
  Age int
}
```
{% endfolding %}
**gorm标签属性值：**

![Rjjpxe](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/Rjjpxe.png)

## 【模型定义】gorm标签属性值一二

注意：多个属性之间使用`;`(英文的分号)分割两个属性

`-`表示忽略该字段的迁移

`primary_key`默认是auto_increment的

index如果没有指定名字，默认是`idx_表名_列名`

unique_index如果没有指定名字，默认是`uix_表名_列名`

unique给某个列加了约束，那么该列将会默认加上unique_index，名字是该列的名字

**唯一约束和唯一索引在 MySQL 数据库里区别：**

1. 概念上不同，约束是为了保证数据的完整性，索引是为了辅助查询；
2. **创建唯一约束时，会自动的创建唯一索引；**
3. 在理论上，不一样，在实际使用时，基本没有区别。

关于第二条，MySQL 中唯一约束是通过唯一索引实现的，为了保证没有重复值，在插入新记录时会再检索一遍，怎样检索快，当然是建索引了，所以，在创建唯一约束的时候就创建了唯一索引

## 【关联表】一对一回顾

![BabrVG](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/BabrVG.png)

一对一关联字段可以建在第一个表也可以建在第二个表。

## 【关联表】一对多回顾

一对多的案例：

1. 一个作者可以有多篇文章，一篇文章只能有一个作者。

2. 一个家庭只能有两个房产证，一个房产证只能属于一个家庭
3. 一个用户可以有多台车，但是一台车只可以属于一个用户

解决方案：如何关联呢？只可以将外键存放到多的一方这个表中

## 【关联表】多对多回顾

多对多的案例：

1. 文章和标签：一个文章可以有多个标签，一个标签可以属于多个文章
2. 用户和客服：一个用户可以有多个客服，一个客服可以服务于多个用户

解决方案：通过中间表进行解决。![HHFiq7](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/HHFiq7.png)

## 【一对一】一对一属于
![h2t1DE](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/h2t1DE.png)
{% folding green, 对应的代码 %}


```go
package main

import "gorm_demo/conn"

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 6:08 下午
 * @Desc:
 **/

type User struct {
   Id   int
   Name string
   Age  int
   Addr string
}

//用户详细信息
type UserProfile struct {
   Id    int
   Pic   string //图片的地 址
   CPic  string //存的是地址
   Phone string

   //方式一：默认的一对一包含
   //这个User不是一个字段而是一个关联关系
   //User   User //关联关系
   //UserID int  //默认关联字段，格式：另外一个model名称全称然后加上ID

   //方式二：可以自己指定关联的字段通过gorm提供的标签进行指定：`gorm:"ForeignKey:当前表的字段名称;AssociationForeignKey:另一个表的字段名称"`
   User User `gorm:"ForeignKey:UId;AssociationKey:Id"` //默认关联字段，格式：另外一个model名称全称然后加上ID //关联关系
   UId  int
}

func main() {
   db := conn.GetConn()
   defer db.Close()

   var user User

   db.AutoMigrate(&User{}, &UserProfile{})
}


```

{% endfolding %}
注意：两种方式建立的外键并不会在数据库里面去建立一个外键体现出来。也就是不会在数据库里面绑定一个外键约束

## 【一对一】一对一包含

属于和包含的区别就是：关联关系和外键字段(关系字段，也就是我们ForeignKey指定的那个字段)都在一方，但是在包含里面是在不同的方里面

也就是在上面那个案例中，我们的UserProfile(关联关系所在的表)是属于另外一个表的，因为两个都在一个model中定义

但是在这里我们的UserProfile是包含另一个表的，因为外键定义在了另外一个表，也就是关联关系与我们的外键字段不在一个model里面，因此就是包含


属于：关系和外键在同一方，有关系的那一方属于另外一个模型
包含：关系和外键不在同一方，有关系的那一方包含另外一个模型
A属于B,关系和外键在同一方，关系和外键在同一方，有关系的那一方（A）属于另外一个模型(B)
A包含B,关系和外键不在同一方，关系和外键不在同一方，有关系的那一方（A）包含另外一个模型(B)
{% folding green, 对应的代码 %}


```go
package main

import "gorm_demo/conn"

/**
 * @Author: yirufeng
 * @Date: 2021/3/23 9:54 下午
 * @Desc: 一对一的包含
 **/

type User struct {
   Id   int
   Name string
   Age  int
   Addr string
   PId  int //外键在另外一方
}

type UserProfile struct {
   Id    int
   Pic   string
   CPic  string
   Phone string

   //关系在这一方,外键在另一方
   User User `gorm:"ForeignKey:PId;AssociationForeignKey:Id"`
}

func main() {
   db := conn.GetConn()
   defer db.Close()
   var user User

   db.AutoMigrate(&User{}, &UserProfile{})
}

```
{% endfolding %}
## 【一对一】一对一小结
1. 一对一的话关系可以放在任意一方，
2. 外键放在与关系一方就是属于，否则就是包含，AssociationKey是和外键不在同一个表的另外一个表的主键
属于：关系和外键在同一方，有关系的那一方属于另外一个模型
包含：关系和外键不在同一方，有关系的那一方包含另外一个模型


通常建议还是要标签指定我们的两个字段的值一个是ForeignKey另外一个是AssociationForeignKey

## 【一对多】一对多的属于和包含

一对多外键一定是放到多的那一方，但是关系放到哪里呢？如果与外键放到一起就是属于否则就是包含

假设有作者表与文章表

一个作者可以有多个文章，因此外键一定在文章

此时关系是可以放到作者的也是可以放到文章的，
如果关系字段放到作者，那么是一个文章的切片 ![ylGXjz](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/ylGXjz.png)
如果关系字段放到文章中，那么是一个作者切片![VLaVr6](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/VLaVr6.png)

## 【多对多】模型定义

在两个表中的其中一个表对应的外键字段(切片)并在后面加上标签`gorm:"many2many:中间表名称;"`

可以指定外键以及关联字段的名称：`gorm:"many2many:中间表名称;ForeignKey:可以是当前表也可以是另外一个表的主键;AssociationForeignKey:只能是除了ForeignKey的另外一个表的主键"`

注意：如果我们两个model的序号不是Id，而是别的名字，我们要自己加上gorm标签指定是主键
![kyOGNUPIl7modHW](https://i.loli.net/2021/03/24/kyOGNUPIl7modHW.png)

## 【一对一操作】增加数据
create的时候是会给我们进行一个关联插入的，也就是创建userProfile的时候指定的属性User我们是没有办法确定user的id的值，
而且我们也没有必要写，因为Gorm插入的时候是会关联插入的，也就是首先会在user_profile中插入一条记录，
然后再插入user表的一条记录，并将user_profile的id作为新插入user的外键
{% folding green, 对应的代码 %}
```go
package main

import conn "gorm_demo/util"

/**
 * @Author: yirufeng
 * @Date: 2021/3/24 9:26 上午
 * @Desc: 一对一增加数据
 **/

type User struct {
   Id   int
   Name string
   Age  int
   Addr string
   PId  int //外键在另外一方
}

type UserProfile struct {
   Id    int
   Pic   string
   CPic  string
   Phone string

   //关系在这一方,外键在另一方
   User User `gorm:"ForeignKey:PId;AssociationForeignKey:Id"`
}

func main() {
   db := conn.GetConn()
   defer db.Close()

   user_profile := UserProfile{
      Pic:   "/root/1.png",
      CPic:  "/root/1.png",
      Phone: "13934999999",
      User: User{
         //由于Id不知道，因为我们是要关联插入，也就是先插入UserProfile，然后同时向user插入一条记录并绑定
         Name: "yirufeng",
         Age:  20,
         Addr: "北京市西城区",
         //PId: 我们其实也不知道PId的值，因为我们user_profile还没有插入
      },
   }
   
   //插入的时候会关联进行插入
   db.Create(&user_profile)
}

```
{% endfolding %}
## 【一对一操作】关联查询

1. 第一种方式-association：![mQfO9lPRi1M5rVH](https://i.loli.net/2021/03/24/mQfO9lPRi1M5rVH.png)
2. 【推荐，比较简单】第二种方式-preload：![FipAC5N3awvVhTk](https://i.loli.net/2021/03/24/FipAC5N3awvVhTk.png)
3. 第三种方式-related：
注意：查看对应的sql语句：我们可以调用Debug()方法让其打印sql语句

{% folding green, 对应的代码 %}


```go
package main

import (
   "fmt"
   "gorm_demo/conn"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/24 9:46 上午
 * @Desc:
 **/

type User struct {
   Id   int
   Name string
   Age  int
   Addr string
   PId  int //外键在另外一方
}

type UserProfile struct {
   Id    int
   Pic   string
   CPic  string
   Phone string

   //关系在这一方,外键在另一方
   User User `gorm:"ForeignKey:PId;AssociationForeignKey:Id"`
}

func main() {
   db := conn.GetConn()
   defer db.Close()


   //关联查询第一种方式：使用asssociation
   var userProfile UserProfile

   db.First(&userProfile, 1)
   fmt.Println(userProfile)
   //注意这里Association里面传入的参数是字段名，
   //另外需要注意的就是我们关联字段查询出的是给对应的关联字段赋值
   db.Model(&userProfile).Association("User").Find(&userProfile.User)
   fmt.Println(userProfile)
   
   //关联查询第二种方式：使用preload
   var userProfile2 UserProfile

   //Preload参数必须是关联关系名字
   //后面可以用First也可以使用Find
   db.Debug().Preload("User").First(&userProfile2, 1)
   fmt.Println(userProfile2)

   //关联查询第三种方式：
   var userProfile3 UserProfile
   db.First(&userProfile3, 1)
   var user User
   db.Model(&userProfile3).Related(&user, "User")
   fmt.Println(userProfile3, user)  //此时userProfile3的User字段全部为空，user是我们查出来的结果

}
```
{% endfolding %}
## 【一对一操作】更新数据
> 这里讲的是通过UserProfile对User进行更新

注意：如果更新多个字段需要对模型初始化

{% folding green, 对应的代码 %}


```go
package main

import (
   "fmt"
   "gorm_demo/学习/conn"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/24 6:42 下午
 * @Desc:
 **/

type User struct {
   Id   int
   Name string
   Age  int
   Addr string
   PId  int //外键在另外一方
}

type UserProfile struct {
   Id    int
   Pic   string
   CPic  string
   Phone string

   //关系在这一方,外键在另一方
   User User `gorm:"ForeignKey:PId;AssociationForeignKey:Id"`
}

func main() {
   db := conn.GetConn()
   defer db.Close()

   //关联查询第二种方式：使用preload
   var userProfile2 UserProfile

   //Preload参数必须是关联关系名字
   //后面可以用First也可以使用Find
   db.Debug().Preload("User").First(&userProfile2, 1)
   fmt.Println(userProfile2)

   //查询到之后对User进行更新
    //更新单个字段
   //更新user_profile
   db.Model(&userProfile2.User).Update("Addr", "北京海淀区")
   fmt.Println(userProfile2)

   //如果要更新多个字段
   db.Model(&userProfile2.User).Update(&User{Name: "未来城", Age: 30})
   fmt.Println(userProfile2)

}
```
{% endfolding %}
## 【一对一操作】删除数据
> 通过UserProfile对User中的数据进行删除
{% folding green, 对应的代码 %}


```go
package main

import (
   "fmt"
   "gorm_demo/学习/conn"
   "log"
)

/**
 * @Author: yirufeng
 * @Date: 2021/3/24 6:44 下午
 * @Desc:
 **/

type User struct {
   Id   int
   Name string
   Age  int
   Addr string
   PId  int //外键在另外一方
}

type UserProfile struct {
   Id    int
   Pic   string
   CPic  string
   Phone string

   //关系在这一方,外键在另一方
   User User `gorm:"ForeignKey:PId;AssociationForeignKey:Id"`
}

func main() {
   db := conn.GetConn()
   defer db.Close()

   //关联查询第二种方式：使用preload
   var userProfile2 UserProfile

   //Preload参数必须是关联关系名字
   //后面可以用First也可以使用Find
   db.Debug().Preload("User").First(&userProfile2, 1)
   fmt.Println(userProfile2)

   log.Println("----------------------------删除数据----------------------------")
   db.Debug().Delete(&userProfile2.User)

}
```
{% endfolding %}

## 【一对多操作】插入数据

注意：方式二中因为多那方的数据是已经在数据库中了，并且一那方的数据不会重复插入。


两种方式推荐使用第一种方式，因为第二种方式可读性不是很好，也就是会让人以为插入两次同样的数据。
{% folding green, 对应的代码 %}


```go
package main

import "gorm_demo/学习/conn"

/**
 * @Author: yirufeng
 * @Date: 2021/3/24 7:00 下午
 * @Desc: 一对多操作
 **/

//分类
type Category struct {
   Id   int
   Name string

   Articles []Article `gorm:"ForeignKey:CId;AssociationKey:Id"` //所以到时候我们就可以通过Category来操作文章了
}

//文章
type Article struct {
   Id      int
   Title   string
   Content string
   Desc    string

   //外键在这里
   CId int
}

func main() {
   db := conn.GetConn()
   defer db.Close()

   db.AutoMigrate(&Category{}, &Article{})

   //方式一：关联添加
   cate := Category{
      Name: "IT",
      Articles: []Article{
         {
            Title: "python权威指南",
            Content: "python基础库以及第三方包",
            Desc: "python进行全面讲解",
         },
         {
            Title: "beego权威指南",
            Content: "beego基础库以及第三方包",
            Desc: "beego进行全面讲解",
         },
      },
   }
   db.Debug().Create(&cate)

   //方式二：我们先对已经有的文章数据(也就是数据已经在文章中了)关联到对应的分类
   article1 := Article{
      Title: "beego权威指南",
      Content: "beego基础库以及第三方包",
      Desc: "beego进行全面讲解",
   }
   article2 := Article{
      Title: "beego权威指南",
      Content: "beego基础库以及第三方包",
      Desc: "beego进行全面讲解",
   }

   db.Create(&article1)
   db.Create(&article2)

   cate2 := Category{
      Name: "IT",
      Articles: []Article{
         //注意：此时article1与article2都在数据库中了，
         //但是此时这里要插入cate2的话我们不会再重复插入article1与article2
         article1,
         article2,
      },
   }
   db.Debug().Create(&cate2)

}
```
{% endfolding %}
## 【一对多操作】三种查询方式

1. 方式1：![2awFkY4uB5Gbhdv](https://i.loli.net/2021/03/24/2awFkY4uB5Gbhdv.png)
2. 方式2：![ltZoYuOhKrbjAeg](https://i.loli.net/2021/03/24/ltZoYuOhKrbjAeg.png)
3. 方式3：![aPksnfv5jeGEAph](https://i.loli.net/2021/03/24/aPksnfv5jeGEAph.png)

## 【一对多操作】更新数据


注意：
1. 一对多进行更新的时候，如果不加上where条件，那么将会更新全部数据。一般我们都会加上where条件进行过滤。

![TFAI25ExBPCDqQ7](https://i.loli.net/2021/03/24/TFAI25ExBPCDqQ7.png)
## 【一对多操作】删除数据


注意：一对多进行数据更新和删除的时候，一定要记得使用`where`条件对数据进行过滤，之后再修改或删除。
![kOpwxo8zabh52Vi](https://i.loli.net/2021/03/24/kOpwxo8zabh52Vi.png)

## 【多对多操作】增加数据

![s5hfZWVXYTCIADQ](https://i.loli.net/2021/03/24/s5hfZWVXYTCIADQ.png)
![HOiBsYAIx316PXQ](https://i.loli.net/2021/03/24/HOiBsYAIx316PXQ.png)

多对多的模型定义：![qmeVWh6M1wFOHnr](https://i.loli.net/2021/03/24/qmeVWh6M1wFOHnr.png)
![8LsfByThIeSbD2R](https://i.loli.net/2021/03/24/8LsfByThIeSbD2R.png)

![4xYsihtfw6pONQ5](https://i.loli.net/2021/03/24/4xYsihtfw6pONQ5.png)
![Dl1Z7rP8LvyXEUT](https://i.loli.net/2021/03/24/Dl1Z7rP8LvyXEUT.png)


修改已有文章的引用：![3EBpU9wvQudnPmL](https://i.loli.net/2021/03/24/3EBpU9wvQudnPmL.png) + ![CoxXzckwN1Vp6sP](https://i.loli.net/2021/03/24/CoxXzckwN1Vp6sP.png)
## 【多对多操作】三种查询方式

related不演示了

preload：![Qi1Fd7jzcm5vwhV](https://i.loli.net/2021/03/24/Qi1Fd7jzcm5vwhV.png)
association：![YgQDNEjik8GyZWl](https://i.loli.net/2021/03/24/YgQDNEjik8GyZWl.png)

association分成两个容器去存储，因此前端渲染中我们采用related与preload多一些，不然还得根据外键去查找

## 【多对多操作】更新操作

![Fo28VylqTK5YCrN](https://i.loli.net/2021/03/24/Fo28VylqTK5YCrN.png) + ![9HL7sAtnvMF8mz2](https://i.loli.net/2021/03/24/9HL7sAtnvMF8mz2.png)

update也可以传入一个结构体来更改多个字段

## 【多对多操作】删除操作
![fJ5GDP](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/fJ5GDP.png)

{% noteblock,  %}


{% endnoteblock %}


{% note info,  %}

{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 标题（可选） %}

Windows 10不是為所有人設計,而是為每個人設計

{% endnoteblock %}

