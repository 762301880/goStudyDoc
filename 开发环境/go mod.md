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

