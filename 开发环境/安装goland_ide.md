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

# 手动重置试用期

### 手动重置（无插件不推荐）

关闭 GoLand，删除：

```shell
C:\Users\你的用户名\AppData\Local\JetBrains\GoLand2026.1
C:\Users\你的用户名\AppData\Roaming\JetBrains\GoLand2026.1
```

###  正确做法（只清试用，保留配置）

关闭所有 JetBrains 进程（包括后台守护）。

只删 **Roaming** 下的试用数据（保留配置）：

```shell
C:\Users\你的用户名\AppData\Roaming\JetBrains\GoLand2026.1\eval
```

清理注册表（可选，更彻底）：

```shell
注册表 → HKEY_CURRENT_USER\SOFTWARE\JavaSoft\Prefs\jetbrains
```

重启 GoLand，会重新获得 30 天试用。

1. 重启 GoLand，会重新获得 30 天试用。

### 对比：两种方式的区别

| 做法                      | 效果                             | 风险       |
| ------------------------- | -------------------------------- | ---------- |
| 只删 eval 目录            | 重置试用，保留所有配置           | 低，推荐   |
| 删整个 Local/Roaming 目录 | 重置试用，清空配置 / 插件 / 缓存 | 高，很折腾 |

### 整理为.bat批量处理

桌面新建文本文档，粘贴下面代码，后缀改成 `重置试用.bat`

使用方法

1. **完全关闭 GoLand**
2. 右键脚本 → **以管理员身份运行**
3. 运行完毕，打开 GoLand
4. `Help → Register` 查看：剩余 30 天

```shell
@echo off
chcp 65001
echo ======================================
echo  一键重置 GoLand2026.1 试用期
echo ======================================
echo.

:: 关闭可能残留的goland进程
taskkill /f /im goland64.exe >nul 2>&1
taskkill /f /im goland.exe >nul 2>&1

:: 删除试用eval目录
set "evalPath=%APPDATA%\JetBrains\GoLand2026.1\eval"
if exist "%evalPath%" (
    rmdir /s /q "%evalPath%"
    echo [√] 已清除试用记录文件夹
) else (
    echo [×] 未检测到试用记录文件夹
)

:: 删除注册表试用残留
reg delete "HKCU\SOFTWARE\JavaSoft\Prefs\jetbrains" /f >nul 2>&1
echo [√] 已清除注册表授权残留

echo.
echo 重置完成！直接打开 GoLand 即可恢复30天试用
pause
```

