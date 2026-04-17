## linux安装

### 请自行官网下载安装包



**linux桌面配置图标**

```shell
# goland.desktop
[Desktop Entry]
Type=Application
Version=2020.3
GenericName=goland
Name=goland
Comment=goland ide
Exec="/home/yaoliuyang/Documents/app/ide/GoLand-2022.3.3/bin/goland.sh"   
Icon=/home/yaoliuyang/Documents/app/ide/GoLand-2022.3.3/bin/goland.svg
Terminal=false
Categories=Development;IDE;
StartupNotify=true
```



##  goland无法执行单个文件

goland报错 Error: Package go_study contains more than one main function Consider using File kind instead

这个报错**只在 GoLand 里运行时出现**，命令行 `go run` 反而没问题，是因为 **GoLand 默认把整个文件夹当成一个程序运行**，但你文件夹里有**多个带 main 函数的文件**。

我给你最直接、最快的 2 种解决方法（不用改代码、不用拆文件夹）：

方法 1：直接用 GoLand 的「File 模式」运行（最简单）

这就是报错提示里说的 **Use File kind instead**（用文件模式运行）。

### 操作步骤：

1. 打开你要运行的那个 `.go` 文件（比如 `demo1.go`）

2. 点击代码编辑区**右上角的运行配置下拉框**

3. 选择 **Edit Configurations...**

4. 在弹出窗口里找到：

   Run kind → 把 Package改成 File

5. 点击 **Apply → OK**

6. 再点运行，**报错立刻消失**

为什么会报错？

- GoLand 默认：**运行整个包（package）**
- 你的文件夹里：**多个文件都有 package main + func main ()**
- Go 不允许一个包里有多个 main 函数 → 报错

总结（一句话记住）

**GoLand 里运行单个带 main 的文件，一定要用 File 模式，不要用 Package 模式！**