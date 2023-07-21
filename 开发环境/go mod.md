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

![image-20230721180811490](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230721180811490.png)

**解决方案**

> GoLand 点击左上角： GoLand -> Preferences -> Go -> Go Modules  
>
> 勾选后点击确认即可

![image-20230721181110341](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230721181110341.png)