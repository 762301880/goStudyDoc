##  GORM文档参考

| 名称                           | 地址                                          |
| ------------------------------ | --------------------------------------------- |
| GORM中文文档                   | [link](https://gorm.io/zh_CN/docs/index.html) |
| laravel_学院 **GORM 中文文档** | [link](https://learnku.com/docs/gorm/v2)      |



## 主流企业方案

通常会用：

- ORM：GORM（最常见）
- 数据库驱动：go-sql-driver/mysql
- 架构：分层（controller / service / dao）

## 标准项目结构

企业一般不会这样写：

❌ 错误写法（新手常见）

```go
func GetUser(c *gin.Context) {
    db.Query("SELECT * FROM user")
}
```

而是👇

```go
project/
 ├── main.go
 ├── config/
 ├── model/      // struct
 ├── dao/        // 数据库操作
 ├── service/    // 业务逻辑
 ├── controller/ // gin handler
```

## 一步一步正确接入 MySQL

### 安装依赖

```go
go get -u gorm.io/gorm
go get -u gorm.io/driver/mysql
```

### 初始化数据库连接（config/db.go）

```go
package config

import (
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)

var DB *gorm.DB

func InitDB() {
    dsn := "user:password@tcp(127.0.0.1:3306)/test?charset=utf8mb4&parseTime=True&loc=Local"

    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
    if err != nil {
        panic("数据库连接失败")
    }

    DB = db
}
```

### 定义模型（model/user.go）

```php
package model

//	type User struct {
//		ID       int
//		Username string
//		Password string
//	}
type User struct {
	ID       int    `json:"id" gorm:"column:id"`
	Username string `json:"username" gorm:"column:username"`
	Password string `json:"password" gorm:"column:password"`
}
```

### DAO 层（操作数据库）

> **dao/userDao.go**

```go
package dao

import (
	"gin_study/config"
	"gin_study/model"
)

func GetUsers() ([]model.User, error) {
	var users []model.User
	err := config.DB.Find(&users).Error
	return users, err
}

func CreateUser(user *model.User) error {
	err := config.DB.Create(user).Error
	return err
}

func GetUserById(id int) (*model.User, error) {
	var user model.User
	err := config.DB.First(&user, id).Error
	return &user, err
}
func UpdateUser(user *model.User) error {
	err := config.DB.Save(user).Error
	return err
}
func DeleteUser(id int) error {
	err := config.DB.Delete(&model.User{}, id).Error
	return err
}
```

### Service 层（业务逻辑）

```go
package service

import "your_project/dao"

func GetUserList() interface{} {
    users, err := dao.GetUsers()
    if err != nil {
        return nil
    }
    return users
}
```

### Controller（Gin）

```go
package controller

import (
    "github.com/gin-gonic/gin"
    "your_project/service"
)

func GetUsers(c *gin.Context) {
    data := service.GetUserList()
    c.JSON(200, gin.H{
        "data": data,
    })
}
```

### main.go

```go
package main

import (
    "github.com/gin-gonic/gin"
    "your_project/config"
    "your_project/controller"
)

func main() {
    config.InitDB() //👈 必须先初始化 数据库

    r := gin.Default()

    r.GET("/users", controller.GetUsers)

    r.Run()
}
```

# 企业里还会加的东西（你如果面试会被问）

如果你说“要跟企业一样”，那下面这些通常也会有👇

### ✔ 1. 配置文件（yaml / env）

数据库连接不会写死

### ✔ 2. 日志系统

比如 zap / logrus

### ✔ 3. 连接池配置

```
sqlDB, _ := db.DB()
sqlDB.SetMaxOpenConns(100)
sqlDB.SetMaxIdleConns(10)
```

### ✔ 4. 事务

```
tx := db.Begin()
tx.Create(...)
tx.Commit()
```

### ✔ 5. context 超时控制