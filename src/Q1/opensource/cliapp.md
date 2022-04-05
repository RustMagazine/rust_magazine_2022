# 命令行工具


## adsb_deku： Rust 实现的终端雷达应用

基于 tui-rs，实现在终端即可解码 ADS-B 来监视飞越你家的飞机

> ADS-B : 广播式自动相关监视

“ADS-B 是一种空中交通监视应用，用于传递飞行参数，比如，位置，航迹和
地速等，通过数据链广播模式，在特定的间隔时间内发送，任何空地用户
都可以申请使用这个功能。” (ICAO国际民航组织)

- ADS-B RAD : 广播式自动相关监视 可用于雷达覆盖空域
- ADS-B NRA : 广播式自动相关监视 可用于无雷达覆盖空域
- ADS-B APP : 广播式自动相关监视 可用于机场地面活动区交通监视

[https://github.com/rsadsb/adsb_deku](https://github.com/rsadsb/adsb_deku)

## cargo-mobile: 帮助你在 iOS 和 Andriod 上方便地使用 Rust

这个工具是对 “如何在 iOS 和 Andriod 上使用 Rust ？” 问题的回应。

cargo-mobile 是一个与框架无关的工具，可以生成 Xcode 和 Android Studio 项目文件、在设备上构建和运行、生成项目样板文件以及其他一些事情！目前支持 macOS 和 Linux 。

该工具在 BrainiumLLC 公司内部已经使用了，可以看其[公司博客介绍](https://dev.brainiumstudios.com/2020/11/24/cargo-mobile.html)。注意，这篇文章是两年前的了，但是工具是刚开源，文章还未来得及更新。

```sh
cargo install --git https://github.com/BrainiumLLC/cargo-mobile
```

[https://github.com/BrainiumLLC/cargo-mobile](https://github.com/BrainiumLLC/cargo-mobile)

## argc: 一种处理 `sh/bash` 命令参数的便捷方式

```sh
# demo.sh

# @describe A demo cli

# @cmd Upload a file
# @arg target!                      File to upload
upload() {
    echo "cmd                       upload"
    echo "arg:  target              $argc_target"
}

# @cmd Download a file
# @flag     -f --force              Override existed file
# @option   -t --tries <NUMBER>     Set number of retries to NUMBER
# @arg      source!                 Url to download from
# @arg      target                  Svae file to
download() {
    echo "cmd:                      download"
    echo "flag:   --force            $argc_force"
    echo "option: --tries           $argc_tries"
    echo "arg:    source            $argc_source"
    echo "arg:    target            $argc_target"
}

eval "$(argc -e $0 "$@")"
```

用 Argc 编写命令行程序，只需要做两件事：

- 在注释中描述选项、参数和子命令
- 调用`eval`命令委托Argc为我们处理命令行参数

Argc 根据标签（`@`注释中标记的字段）生成解析规则和帮助文档。


[https://github.com/sigoden/argc](https://github.com/sigoden/argc)

## Pijul 1.0 beta 发布

Pijul 是一个免费的开源 (GPL2)分布式版本控制系统。

经过了 53 个alpha版本的Pijul 1.0 beta终于发布了

- [https://pijul.org/](https://pijul.org/)
- [https://nest.pijul.com/pijul/pijul](https://nest.pijul.com/pijul/pijul)

## 获取系统信息的命令行工具汇总

[neofetch](https://github.com/dylanaraps/neofetch)是一个用`bash 3.2+`编写的命令行系统信息工具；

以下是一些使用Rust编写的命令行工具的替代品：

- [ferris-fetch](https://github.com/irevenko/ferris-fetch) - A system information tool for Rustaceans.
- [freshfetch](https://github.com/K4rakara/freshfetch) - A fresh take on neofetch.
- [fsi](https://github.com/MustafaSalih1993/fsi) - FSI (Fetch System Info) cli tool written in Rust.
- [macchina](https://github.com/Macchina-CLI/macchina) - A system information fetcher, with an emphasis on performance and minimalism.
- [onefetch](https://github.com/o2sh/onefetch) - Git repository summary on your terminal.
- [rfetch](https://github.com/kamui-fin/rfetch) - A fast and minimal fetch program.
- [rsfetch](https://github.com/Phate6660/rsfetch) - A WIP rewrite of rsfetch from scratch.
- [scrftch](https://github.com/wezm/scrftch) - Screenfetch in Rust.

## Puff——一个基于Rust的CLI工具，用于将应用程序的配置存储在一个公共位置，以便轻松传输到另一台机器

Puff是一个CLI工具，用于管理正在开发的应用程序的配置文件。通常，这些配置与项目的源代码一起使用。由于它们可能包含的密码/密钥，它们通常被排除在版本控制系统之外。Puff管理这些文件并将它们存储在一个公共位置，从而更容易将它们传输到另一台开发机器。你的应用程序通过Puff创建的符号链接访问配置文件。

[https://crates.io/crates/puff](https://crates.io/crates/puff)

## jless：命令行 JSON 查看器

JLess 是一个命令行 JSON 查看器，专为阅读、探索和搜索 JSON 数据设计。

- 美化输出 JSON 并附加语法高亮。适合在探索外部 API 或调试请求有效载荷时使用。
- 展开/折叠对象和数组，有助于掌握 JSON 文档的高级/低级结构。得益于大量受 Vim 启发的命令，探索数据变得轻而易举。
- 支持基于表达式的全文搜索。能够在长字符串值中快速查找数据，或者在相同对象键之间跳转。

[https://github.com/PaulJuliusMartinez/jless](https://github.com/PaulJuliusMartinez/jless)

## zellij - tmux 的 Rust 实现版本

Zellij是一个面向开发人员、面向运维的人员和任何喜欢终端的人的工作区。它的核心是一个终端多路复用器（类似于tmux和screen），但这仅仅是它的基础设施层。

Zellij 包括一个布局系统和一个插件系统，允许人们以任何可以编译为 WebAssembly 的语言创建插件。


[https://github.com/zellij-org/zellij](https://github.com/zellij-org/zellij)

## Zee - Rust编写的终端编辑器

Zee 是一个现代的终端编辑器，由 Rust 编写。

[https://github.com/zee-editor/zee](https://github.com/zee-editor/zee)

## Fig.io 使用 Rust 重写其 CLI

Fig 为现有终端添加 IDE 风格的自动补全。

此前，Fig 是一个捆绑了 CLI 的桌面 App 。而现在，Fig 用 Rust + Clap 进行了重现，变成一个可以添加到桌面 App 中的 CLI 工具。这意味着：

跨平台体验，支持 Linux 和 Windows，更好的性能
在远程计算机上运行
发布酷炫的新产品
同时，Fig 也为 Rustacean 提供 System Engineer 职位。

[https://fig.io/](https://fig.io/)

## xshell: 0.2 版本发布

xshell提供了一套跨平台实用工具，用于编写跨平台和符合人体工程学的 bash 脚本。

[https://github.com/matklad/xshell](https://github.com/matklad/xshell)

## igrep - 交互式grep

[https://github.com/konradsz/igrep](https://github.com/konradsz/igrep)