##  [官方包仓库](https://pkg.go.dev)



## [kardianos](https://github.com/kardianos)/**[govendor](https://github.com/kardianos/govendor)**  包管理工具

**参考资料**

| 名称 | 地址                                           |
| ---- | ---------------------------------------------- |
| 博客 | [link](https://zhuanlan.zhihu.com/p/103914406) |



##  Go-MySQL 包-go-sql-driver

| name               | url                                                          |
| ------------------ | ------------------------------------------------------------ |
| go-sql-drive包地址 | [link](https://pkg.go.dev/github.com/go-sql-driver/mysql#section-readme) |

### 安装

> 使用shell 中的[go 工具](https://golang.org/cmd/go/)简单地将软件包安装到[$GOPATH](https://github.com/golang/go/wiki/GOPATH)中：

```shell
go get -u github.com/go-sql-driver/mysql
```

**基本使用**

```go
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	"log"
)

const (
	dbDriver = "mysql"
	dbUser   = "root"          //your_username
	dbPass   = "123456"        //your_password
	dbName   = "laravel_study" //your_database_name
)

func main() {
	// 连接数据库
	db, err := sql.Open(dbDriver, fmt.Sprintf("%s:%s@/%s", dbUser, dbPass, dbName))
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()

	// 测试连接
	err = db.Ping()
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Connected to the database")

	//// 创建表
	//createTableQuery := `
	//	CREATE TABLE IF NOT EXISTS users (
	//		id INT AUTO_INCREMENT PRIMARY KEY,
	//		username VARCHAR(50) NOT NULL,
	//		email VARCHAR(100) NOT NULL
	//	)
	//`
	//_, err = db.Exec(createTableQuery)
	//if err != nil {
	//	log.Fatal(err)
	//}
	//fmt.Println("Table created successfully")

	// 插入数据
	insertDataQuery := "INSERT INTO users (username, email) VALUES (?, ?)"
	_, err = db.Exec(insertDataQuery, "user1", "user1@example.com")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Data inserted successfully")

	// 查询数据
	query := "SELECT id, username, email FROM users"
	rows, err := db.Query(query)
	if err != nil {
		log.Fatal(err)
	}
	defer rows.Close()

	fmt.Println("Query results:")
	for rows.Next() {
		var id int
		var username, email string
		err := rows.Scan(&id, &username, &email)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Printf("ID: %d, Username: %s, Email: %s\n", id, username, email)
	}

	//// 更新数据
	//updateDataQuery := "UPDATE users SET email = ? WHERE username = ?"
	//_, err = db.Exec(updateDataQuery, "new_email@example.com", "user1")
	//if err != nil {
	//	log.Fatal(err)
	//}
	//fmt.Println("Data updated successfully")

	//// 删除数据
	//deleteDataQuery := "DELETE FROM users WHERE username = ?"
	//_, err = db.Exec(deleteDataQuery, "user1")
	//if err != nil {
	//	log.Fatal(err)
	//}
	//fmt.Println("Data deleted successfully")
}

```

**添加地址&驱动**

> 在Go中，`github.com/go-sql-driver/mysql`包是一个MySQL数据库驱动，它主要用于连接MySQL数据库和执行SQL操作。该包的主要作用是通过DSN（数据源名称）来指定数据库的连接信息，包括数据库的IP地址、端口、用户名、密码等。
>
> DSN的格式通常为：
>
> > MySQL DSN的全称是数据源名称（Data Source Name），它是一种标识数据源的名称，可以MySQL DSN的全称是数据源名称（Data Source Name），它是一种标识数据源的名称，可以方便地连接到数据库。更具体来说，MySQL的DSN字符串格式示例为：`"<username>:<password>@tcp(<hostname>:<port>)/<database_name>?charset=utf8mb4&parseTime=True&loc=Local"`。其中，`<username>`是MySQL服务器的用户名，`<password>`是MySQL服务器的密码，`<hostname>`是MySQL服务器的主机名或IP地址，`<port>`是MySQL服务器的端口号，`<database_name>`是要连接的数据库名称。
>
> ```shell
> username:password@protocol(address)/dbname?param=value
> ```
>
> 其中，`address`部分就包括了IP地址。所以，你可以通过修改DSN中的`address`部分来更改MySQL数据库的IP地址。下面是一个简单的示例：

```go
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/go-sql-driver/mysql"
)

const (
	dbDriver = "mysql"
	dbUser   = "your_username"
	dbPass   = "your_password"
	dbName   = "your_database_name"
	dbHost   = "new_ip_address" // 修改为新的IP地址
	dbPort   = "3306"            // 修改为新的端口号
)

func main() {
	// 构建新的DSN
	dsn := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s", dbUser, dbPass, dbHost, dbPort, dbName)

	// 连接数据库
	db, err := sql.Open(dbDriver, dsn)
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()

	// 其他数据库操作...
}
```

> 在上面的示例中，你需要将`new_ip_address`替换为你想连接的新MySQL数据库的IP地址，并根据需要修改端口号。然后，通过构建新的DSN来连接数据库。
>
> 请注意，这只是一个示例，实际应用中你可能还需要根据需要进行其他配置项的调整。在修改连接信息时，确保考虑到网络安全性和访问权限等因素。

## 定时任务

###  robfig/cron

**安装**

```go
go get github.com/robfig/cron/v3@v3.0.0
```

**推荐结构**

```shell
.
├── main.go
├── cron/
│   ├── cron.go        // 初始化 cron
│   └── jobs.go        // 所有定时任务
└── router/
    └── router.go
```

**cron 初始化（cron/cron.go）**

```go
package cron

import (
	"github.com/robfig/cron/v3"
)

// InitCron 初始化 cron 调度器
func InitCron() *cron.Cron {
	// 开启秒级 + 防止任务重叠执行
	c := cron.New(
		cron.WithSeconds(),
		cron.WithChain(
			cron.SkipIfStillRunning(cron.DefaultLogger), // 上一个没执行完就跳过
		),
	)

	// 注册所有任务
	RegisterJobs(c)

	// 启动
	c.Start()

	return c
}
```

**任务定义（cron/jobs.go）**

```go
package cron

import (
	"fmt"
	"time"

	"github.com/robfig/cron/v3"
)

// RegisterJobs 注册所有定时任务
func RegisterJobs(c *cron.Cron) {

	// 👉 每5秒
	_, _ = c.AddFunc("*/5 * * * * *", JobEvery5Sec)

	// 👉 每分钟
	_, _ = c.AddFunc("0 * * * * *", JobEveryMinute)

	// 👉 每天凌晨2点
	_, _ = c.AddFunc("0 0 2 * * *", JobDaily)
}

// ================= 具体任务 =================

// JobEvery5Sec 每5秒执行
func JobEvery5Sec() {
	fmt.Println("[cron] 每5秒任务:", time.Now())
}

// JobEveryMinute 每分钟执行
func JobEveryMinute() {
	fmt.Println("[cron] 每分钟任务")
}

// JobDaily 每天凌晨任务
func JobDaily() {
	fmt.Println("[cron] 每日任务")
}
```

**Gin 路由（router/router.go）**

```go
package router

import "github.com/gin-gonic/gin"

func InitRouter() *gin.Engine {
	r := gin.Default()

	r.GET("/", func(ctx *gin.Context) {
		ctx.JSON(200, gin.H{
			"msg": "Gin + cron running",
		})
	})

	return r
}
```

**主入口（main.go）**

```go
package main

import (
	"your_project/cron"
	"your_project/router"
)

func main() {
	// 1️⃣ 启动定时任务
	cron.InitCron()

	// 2️⃣ 启动 Gin
	r := router.InitRouter()
	r.Run(":8080")
}
```

以后你要加任务，只改：

```go
RegisterJobs()
```

## go读取.env

> https://pkg.go.dev/github.com/joho/godotenv

### 安装扩展

```go
go get github.com/joho/godotenv
```

### 使用

**示例.env**

```go
DB_HOST=localhost
DB_PORT=3306
```

**调用示例**

```go
package main

import (
    "fmt"
    "os"

    "github.com/joho/godotenv"
)

func main() {
    // 加载 .env 文件
    err := godotenv.Load()
    if err != nil {
        fmt.Println("Error loading .env file")
    }

    // 读取变量
    dbHost := os.Getenv("DB_HOST")
    fmt.Println("DB_HOST:", dbHost)
}
```

### 封装

**对于配置目录**

```shell
project/
  config/
    env.go
  main.go
```

**核心封装 `env.go`**

```go
package config

import (
	"log"
	"os"

	"github.com/joho/godotenv"
)

func InitEnv() {
	err := godotenv.Load()
	if err != nil {
		log.Println("⚠️ 未找到 .env 文件，使用系统环境变量")
	}
}

// 通用获取方法
func Get(key string) string {
	return os.Getenv(key)
}
```

**使用方式**

```go
package main

import (
	"fmt"
	"your_project/config"
)

func main() {
	config.InitEnv() //必须先初始化 env配置

	dbHost := config.Get("DB_HOST")
	fmt.Println("DB_HOST:", dbHost)
}
```

### 进阶配置

**强类型配置**

```go
package config

import (
	"log"

	"github.com/joho/godotenv"
)

type Config struct {
	DBHost string
	DBPort string
}

var Cfg Config

func Init() {
	err := godotenv.Load()
	if err != nil {
		log.Println("⚠️ 未找到 .env")
	}

	Cfg = Config{
		DBHost: getEnv("DB_HOST", "localhost"),
		DBPort: getEnv("DB_PORT", "3306"),
	}
}
```

```go
func getEnv(key, defaultVal string) string {
	val := os.Getenv(key)
	if val == "" {
		return defaultVal
	}
	return val
}
```

**使用**

```go
config.Init()

fmt.Println(config.Cfg.DBHost)	
```

## 代码热加载(重:只能开发环境中使用)

### 场景为什么需要热加载

> 开发环境每次改代码 都需要**改完 → go run → 重启**  效率很低
>
> 热加载（Hot Reload）

###  Air（Go 热加载神器）

**安装**

```go
go install github.com/air-verse/air@latest
```

**查看版本**

```go
air -v
```

**在项目初始化**

> 在你的项目根目录：

```go
air init
```

会生成：

```go
.air.toml
```

**启动热加载**

```go
air
```

以后你只要：

 改代码 → 保存
 自动重启服务
 不需要 go run 了

**效果**

```go
watching .
building...
running...
```

然后你改任何 `.go` 文件：

✔ 自动编译
 ✔ 自动重启 Gin
 ✔ 浏览器直接刷新就生效

### 为什么生产环境不能用热加载

**总结**

❌ Air / Fresh / CompileDaemon = 只用于开发
 ✅ 生产环境 = 必须编译成二进制直接运行

热加载工具做了这些事：

监听文件变化

- 一旦 `.go` 文件变动就触发 rebuild

生产环境不可能一直监控文件

**动态编译 + 重启进程**

- 每次改动都会 `go build`
- 再 kill + restart 服务

👉 这在生产会导致：

- 服务抖动
- 请求中断
- CPU 飙升





