---
layout: post
title: "Windows Subsystem for Linux (WSL) 最新详细安装教程"
---

WSL（Windows Subsystem for Linux）是微软开发的一项技术，允许用户在Windows系统中直接运行完整的Linux环境，无需虚拟机。通过操作系统级虚拟化，WSL将Linux子系统无缝嵌入Windows，提供原生Linux命令行工具、软件包管理器及应用程序支持。它具有轻量化、文件系统集成、良好的交互性及开发效率提升等优点，消除了Windows与Linux之间的隔阂，尤其适合开发者和需在Windows平台上使用Linux工具的用户。

## 1. 启用 WSL 功能

打开开始菜单，在开始菜单中输入 `启用或关闭 Windows 功能`，在弹出的窗口中勾选 `虚拟机平台` 和 `适用于 Linux 的 Windows 子系统`，确定之后重启系统。

![Windows 功能](/images/wsl/enable-windows-features.png)

重启系统后，打开终端，在终端中输入：

```powershell
wsl.exe --update
```

即可安装 WSL 相关的组件，这一步可能需要几分钟的时间。

**注意**：这一步需要保证 `Windows Update`，`Windows 防火墙`等功能可以正常使用，如果这一步报错`Ox80072ee2` 可看下一步的解决方法。

## 2. 安装 Ubuntu

打开 `Microsoft Store`，搜索 `Ubuntu`并下载。

![Microsoft Store](/images/wsl/microsoft-store-ubuntu.png)

下载完成后，可以在开始菜单中找到 Ubuntu，如果终端使用的是 `Windows Terminal` 那么在选项卡中也能看到 Ubuntu 的选项。

## 3. Windows 10 LTSC 替代安装方案

打开网址：<https://store.rg-adguard.net/>，在网址中输入 <https://apps.microsoft.com/detail/9pdxgncfsczv?rtc=1&hl=zh-cn&gl=CN>，并回车搜索，在弹出的下载结果中，下载后缀名为 `.appxbundle` 的文件。

下载完成后，打开终端，确保使用的是 `Windows Powershell` 而不是 `cmd`，输入以下内容：

```powershell
Add-AppxPackage -Path Ubuntu安装包路径
```

Ubuntu 安装包路径**请写上实际的文件路径**。
回车即可安装。

## 4. 创建新用户和密码

![Ubuntu 1](/images/wsl/wsl-ubuntu-create-user.png){:width="25%"}

这一步需要输入用户名和密码，注意：输密码的界面是**看不到的**，不要认为是哪里出问题了。

在输入完用户名称和密码之后，就代表安装成功了：

![Ubuntu](/images/wsl/wsl-initialized.png)

## 5. 更换镜像源以及初步设置

在 Ubuntu 终端中输入：

```bash
lsb_release -a
```

即可查询当前安装的 Ubuntu 版本

打开 [清华镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)，找到选择 Ubuntu 版本的地方，选择与本机实际安装一致的版本，本文默认安装的是 `Ubuntu 22.04`。

Ubuntu 24.04 及以前版本的软件源所在位置为 `/etc/apt/sources.list`，而在 Ubuntu 24.04中，Ubuntu 官方更改了软件源的位置和形式，新的位置为 `/etc/apt/sources.list.d/ubuntu.sources`。

使用 Ubuntu 自带的 `nano` 程序进行软件源的更换，在终端输入以下内容：

```bash
sudo nano /etc/apt/sources.list
```

用下面的内容完全替换并保存（该内容仅适用于 Ubuntu 22.04）：

```txt
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
```

如果不是 Ubuntu 22.04 **请不要复制上面的内容**，自行前往 [镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/) 复制对应的软件源。

按下 `Ctrl + O` 并回车保存，再按下 `Ctrl + X` 退出 `nano`。

保存之后，输入以下内容更新镜像源：

```bash
sudo apt update && sudo apt upgrade
```

这一步可能需要较长时间，等待更新完成，即代表 Ubuntu 子系统初步设置完成。

## 6. 使用 WSLg

**警告**：对 Linux GUI 的支持需要使用 Windows 10 版本 19044+ 或 Windows 11。

对 GUI 的支持需要 WSL 2，如果当前采用的是 WSL 1 需要升级为 WSL2。在 Windows 终端中输入：

```powershell
wsl -l -v
```

即可检查 WSL 版本，如果输出的是1，输入以下内容转换：

```powershell
wsl --set-default-version 2
```

同时还需要下载 [更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)。下载更新包后，重新打开 Ubuntu，即可使用 GUI 功能，可以随便下载一个 Linux GUI 程序测试，比如 `gnome-text-editor`：

```bash
sudo apt install gnome-text-editor
```

![gnome-text-editor](/images/wsl/gnome-text-editor.png)

同时在开始菜单，也能看到这些 Linux GUI 的应用程序的快捷方式。

## 7. 文件互操作

Windows 上操作 Linux：在 Windows 文件资源管理器左侧可以找到 Linux 的标志，点击 Linux 就可以操作 Linux 文件。

![Windows 操作 Linux 文件](/images/wsl/windows-explorer-linux.png)

Linux 操作 Windows 文件：
Windows 下的所有文件都被挂载在了 `/mnt` 下，在Windows 终端中的任意目录下输入 `wsl`，即可进入 Linux 对应的路径。

![WSL](/images/wsl/wsl-command.png)
