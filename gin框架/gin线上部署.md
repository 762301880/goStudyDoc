

## 生产环境要做什么

上线和本地开发最大区别是：

- ❌ 不能用 debug 模式
- ❌ 不能每次改代码手动 `go run`
- ❌ 要守护进程（崩了自动拉起）
- ❌ 要反向代理（Nginx）
- ❌ 要可观测（日志）

### Gin 生产模式设置

**开启 release 模式**

```go
import "github.com/gin-gonic/gin"

func main() {
    gin.SetMode(gin.ReleaseMode) // 👈 关键
    r := gin.Default()

    r.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{"msg": "ok"})
    })

    r.Run(":8080")
}
```

或者用环境变量：

```shell
export GIN_MODE=release
```

**编译成二进制（不要用 go run）**

```go
go build -o app main.go
```

得到一个文件：

```go
app
```

直接上传到服务器就行。

**服务器运行方式（最简单）**

> 记得使用**supervisor**守护进程

```shell
chmod +x app
./app
```

### docker部署

```dockerfile
FROM golang:1.22

WORKDIR /app
COPY . .

RUN go build -o app

CMD ["./app"]
```

