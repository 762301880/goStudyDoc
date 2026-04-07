# 资料

| 名称                   | 地址                                                         |
| ---------------------- | ------------------------------------------------------------ |
| go modules详解相关命令 | [link](https://learnku.com/courses/go-video/2022/006-go-modules-related-commands/11454) |



## Go Modules

### Go Modules 命令：

| 命令            | 作用                                           |
| :-------------- | ---------------------------------------------- |
| go mod init     | 初始化当前文件夹，创建 go.mod 文件             |
| go mod why      | 解释为什么需要依赖                             |
| go mod edit     | 编辑 go.mod 文件                               |
| go mod vendor   | 将依赖复制到 vendor 目录下                     |
| go mod verify   | 校验依赖                                       |
| go mod graph    | 打印模块依赖图                                 |
| go mod tidy     | 增加缺少的包，删除无用的包                     |
| go mod download | 下载依赖包到本地（默认为 GOPATH/pkg/mod 目录） |

# bug相关

## [解决*go*.*mod文件*中require内依赖全部飘红](https://blog.51cto.com/sdwml/5789358)

> 明明执行了**go mod vendor**但是项目却还是飘红

![image-20230721180811490](https://gitee.com/yao_liu_yang/blogImages/raw/master/blogImages/image-20230721180811490.png)

**解决方案**

> GoLand 点击左上角： GoLand -> Preferences -> Go -> Go Modules  
>
> 勾选后点击确认即可

![image-20230721181110341](https://gitee.com/yao_liu_yang/blogImages/raw/master/blogImages/image-20230721181110341.png)



**go mod** 是 Go 语言从 **1.11** 版本引入、**1.16** 成为默认的官方依赖管理工具，核心是用 `go.mod` 文件定义项目模块与依赖，实现可复现构建与版本管理。

### 📄 核心作用与解决的痛点

- 替代旧的 `GOPATH` 模式，不再强制要求代码放在 `src` 目录，支持任意位置开发。
- 精确记录直接 / 间接依赖版本，解决 “环境不一致”“依赖混乱” 问题，保证团队与 CI/CD 构建一致。
- 遵循**语义化版本（SemVer）**（如 `v1.2.3`），主版本号变更代表不兼容 API 调整。
- 配套 `go.sum` 文件，通过校验和确保依赖未被篡改，提升安全性。

### 📝 go.mod 文件结构（核心指令）

```go
module example.com/myapp       // 模块路径，唯一标识模块，也是包导入前缀
go 1.21                        // 项目兼容的 Go 版本
require (                      // 声明依赖模块及最小版本
    github.com/gin-gonic/gin v1.9.1
    golang.org/x/sync v0.6.0
)
replace (                      // 替换依赖（本地路径/私有镜像），解决私有库/版本冲突
    github.com/old/module => github.com/new/module v2.0.0
)
exclude github.com/xxx v1.0.0  // 排除指定版本依赖
```

### ⚙️ 常用命令

| 命令            | 作用                                                       |
| --------------- | ---------------------------------------------------------- |
| `go mod init`   | 在当前目录生成 `go.mod`（项目初始化）                      |
| `go mod tidy`   | 整理依赖：新增 / 删除未使用依赖，补全 `go.mod` 与 `go.sum` |
| `go get`        | 下载 / 升级依赖（如 `go get github.com/xxx@v1.2.3`）       |
| `go mod vendor` | 将依赖拷贝到 `vendor` 目录，离线构建时使用                 |
| `go mod verify` | 验证依赖的校验和与 `go.sum` 是否一致Go                     |

### 💡 关键概念

- **模块（Module）**：包含 `go.mod` 的目录树，是依赖管理的基本单元。
- **最小版本选择（MVS）**：自动选择满足所有依赖的**最低兼容版本**，避免版本膨胀。
- **伪版本**：未打正式标签时，Go 生成的版本号（如 `v0.0.0-20200330080233-e4ea8bd1cbed`），由时间戳 + Commit ID 组成。

### ✅ 实践建议

- 新项目用 `go mod init 模块路径` 初始化，模块路径建议用代码仓库地址（如 `github.com/用户名/项目名`）。
- 提交代码时务必包含 `go.mod` 与 `go.sum`，确保他人构建一致。
- 依赖冲突用 `replace` 或 `exclude` 调整；升级依赖用 `go get -u`。