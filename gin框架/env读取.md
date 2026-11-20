## 推荐库：`github.com/joho/godotenv`（最常用）

> Go 标准库**没有内置解析.env**，一般用 godotenv，把 .env 里面的键值加载到进程环境变量，之后用 `os.Getenv()` 获取

### [godotenv-github文档](https://github.com/joho/godotenv)

### 1. 安装

```bash
go get github.com/joho/godotenv
```

### 2. 项目根目录新建 `.env`

```bash
# .env
APP_NAME=go-demo
PORT=8080
DB_HOST=127.0.0.1
DB_PASS=123456
```

> ⚠️ 记得在 `.gitignore` 添加 `.env`，不要提交密钥到 git

### 3. main.go 示例

```go
package main

import (
	"fmt"
	"log"
	"os"

	"github.com/joho/godotenv"
)

func main() {
	// 加载当前目录 .env
	err := godotenv.Load()
	// 如果文件不存在，不直接panic（线上环境不一定有.env）
	if err != nil {
		log.Println("警告：未找到 .env 文件，使用系统环境变量")
	}

	// 读取变量
	appName := os.Getenv("APP_NAME")
	port := os.Getenv("PORT")
	dbHost := os.Getenv("DB_HOST")

	fmt.Println("APP_NAME:", appName)
	fmt.Println("PORT:", port)
	fmt.Println("DB_HOST:", dbHost)
}
```

### 4.常用进阶用法

#### 指定其他路径、多个 env 文件

```go

// 加载指定文件
err := godotenv.Load(".env.dev")

// 同时加载多个，后面的不会覆盖前面已存在的环境变量
err := godotenv.Load(".env", ".env.local")
```

#### Overload：强制覆盖已有环境变量（本地调试常用）

```go
// 会覆盖系统里同名的环境变量
err := godotenv.Overload(".env.local")
```

#### 只解析，不注入环境变量（返回 map）

```go
envMap, err := godotenv.Read(".env")
if err != nil {
	log.Fatal(err)
}
fmt.Println(envMap["DB_PASS"])
```

