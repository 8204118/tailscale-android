# Tailscale Android 客户端

https://tailscale.com

让专用 WireGuard® 网络变得简单

## 概述

本仓库包含开源的 Tailscale Android 客户端。

## 使用

#### Tailscale 软件包

最新的稳定版 APK 可以从 [Tailscale 软件包稳定版通道](https://pkgs.tailscale.com/stable/#android) 获取。

不稳定版本可以从 [Tailscale 软件包不稳定版通道](https://pkgs.tailscale.com/unstable/#android) 获取。

这些 APK 包含所有支持的平台和架构。如需安装紧凑型 APK、Android TV，或想要自动更新，请访问 [Google Play 商店](https://play.google.com/store/apps/details?id=com.tailscale.ipn)。

#### Google Play 商店

[<img src="https://play.google.com/intl/en_us/badges/images/generic/en-play-badge.png"
     alt="在 Google Play 获取"
     height="80">](https://play.google.com/store/apps/details?id=com.tailscale.ipn)

帮助我们测试新功能和 bug 修复！Play 商店提供了 [beta 测试通道](https://play.google.com/apps/testing/com.tailscale.ipn)。

#### Amazon 应用商店

可以从 [Amazon 应用商店](https://www.amazon.com/dp/B0D38TRB3N) 下载该应用，适用于 Amazon Fire 平板和 Fire TV 设备。

#### F-Droid

[F-Droid](https://f-droid.org/packages/com.tailscale.ipn/) 项目会构建本仓库中的源代码，并维护独立构建的 APK。请注意，F-Droid 的构建版本并非由 Tailscale 团队发布、更新或验证。

## 准备构建环境

有多种方式可以搭建构建环境。对于长期开发，Android Studio 方式最为实用。

所有方式都需要：

- Go 运行时
- Android SDK
- Android SDK 组件（运行 `make androidsdk` 会自动安装）

### Android Studio

1. 安装 Go 运行时（https://go.dev/dl/）。
2. 安装 Android Studio（https://developer.android.com/studio）。
3. 启动 Android Studio，在欢迎界面选择 "More Actions" 和 "SDK Manager"。
4. 在 SDK Manager 中，选择 "SDK Tools" 选项卡，安装 "Android SDK Command-line Tools (latest)"。
5. 运行 `make androidsdk` 安装所需的 SDK 组件。

如果不想使用 Android Studio，也可以单独安装 Android SDK。Makefile 会自动检测常见路径，因此在 Debian / Ubuntu 系统上 `sudo apt install android-sdk` 即可。如果要使用安装在非标准位置的 Android SDK，请将 `ANDROID_SDK_ROOT` 环境变量设置为 SDK 的路径。

如果你安装了 Android Studio，其工具可能不在 PATH 中。要获取正确的工具路径，请运行 `make androidpath`，并在 shell 中导出其提供的路径。

#### 代码格式化

应使用默认设置下的 ktfmt 插件来自动格式化 Android Studio 中的所有 Java、Kotlin 和 XML 文件。请启用 "Format on Save"（保存时格式化）。

### Docker

如果不想在宿主机上安装软件，可以使用基于 Docker 的开发方式，构建并启动一个 shell：

```sh
make docker-shell
```

还有其他多个 makefile 目标可用于搭建正确的构建环境并运行构建。

请注意，docker makefile 目标会在完成后保留镜像并移除容器。如果构建环境或工具链发生变化，可能需要重新构建缓存的 docker 镜像。docker 构建镜像名称在 makefile 中以参数化形式定义，修改它即可简单实现这一点。

### Nix

如果你安装了 Nix 2.4 或更高版本，可以通过以下方式搭建 Nix 开发环境：

```sh
alias nix='nix --extra-experimental-features "nix-command flakes"'
nix develop
```

该 flake 提供 Java、`make`、`curl`、`git` 等主机工具，并将构建指向 `./android-sdk` 中的仓库本地 Android SDK。该 SDK 目录被 Git 忽略，并在多次构建间复用。

首次使用时，安装 Android SDK 组件：

```sh
make androidsdk
```

然后正常构建：

```sh
make tailscale-debug
```

调试版 APK 会写入 `./tailscale-debug.apk`。

无需进入交互式 shell 的一次性命令：

```sh
nix develop --command make androidsdk
nix develop --command make tailscale-debug
```

如需跳过 `gomobile bind` 步骤、更快地进行仅 Kotlin 的迭代：

```sh
nix develop --command bash -lc 'cd android && ./gradlew ktfmtCheck compileDebugKotlin'
```

## 构建

```sh
make apk
make install
```

## 构建发布版

使用 `make tag_release` 来打上 Play 商店版本号、更新版本名称并标记当前提交。版本号由发布时的墙钟时间（自 Unix 纪元以来的分钟数）推导得出，并提交到 `android/build.gradle`，因此它会在所有构建和分支上单调递增，同时对任何给定提交保持固定。

我们只保证支持最新的 Go 版本以及任何 Go beta 或发布候选构建（当前为 Go 1.14）的模块模式。它在更早的 Go 版本或 GOPATH 模式下也可能可用，但我们不保证其可用性。

## 在 Fire Stick TV 上开发

在 Fire Stick 上：

* 设置 > 我的 Fire TV > 开发者选项 > ADB 调试 > 打开

然后是一些有用的命令：

```
adb connect 10.2.200.213:5555
adb install -r tailscale-fdroid.apk
adb shell am start -n com.tailscale.ipn/com.tailscale.ipn.MainActivity
adb shell pm uninstall com.tailscale.ipn
```

## 问题反馈

关于本代码或托管服务的问题，请提交到 [tailscale 问题跟踪器](https://github.com/tailscale/tailscale/issues)。

## 参与贡献

`under_construction.gif`

欢迎提交 PR，但我们仍在完善贡献流程和工具。

我们要求提交中包含 [开发者原创证书](https://en.wikipedia.org/wiki/Developer_Certificate_of_Origin)
`Signed-off-by` 签名行。

## 关于我们

我们是 [Tailscale](https://tailscale.com)。更多关于我们和我们正在构建的内容，请访问 https://tailscale.com/company。

WireGuard 是 Jason A. Donenfeld 的注册商标。
