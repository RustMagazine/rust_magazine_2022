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