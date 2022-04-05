# 开源观察 | 其他工具

## lapce 发布 0.0.9 版本

lapce 是一款受 Xi-Editor 启发的编辑器，类似于 vscode。作者声称，他对 lapce 抱有雄心壮志，希望有朝一日可以和 VScode 竞争。

目标：

- 启动迅速，基于 Rust + Druid 开发，使用 wgpu 渲染。
- 支持远程开发
- 支持 LSP，支持代码智能提示、诊断等
- Vim 模式支持
- WASI 插件系统
- 内置终端

目前为 0.0.9 版本，看起来还不太完善，可以先关注。

- [https://lapce.dev/](https://lapce.dev/)
- [Reddit 相关讨论](https://www.reddit.com/r/rust/comments/t550wy/lapce_open_source_code_editor_inspired_by_xieditor/)

## arboard: 1Password 开源的跨平台剪切板工具

支持在 Linux、Mac 和 Windows 上以独立于平台的方式复制和粘贴文本和图像数据。

[https://github.com/1Password/arboard](https://github.com/1Password/arboard)

## sys-locale: 1Password 开源的轻量级获取位置的跨平台库

支持 `iOS/ Android/ MacOS/ Linux/ Windows/ WebAssembly` 等平台。

[https://github.com/1Password/sys-locale](https://github.com/1Password/sys-locale)

## Weylus：移动设备作为输入板/触屏

将平板电脑或智能手机用作计算机上的图形输入板 / 触摸屏。

主要特征：

- 使用平板电脑控制鼠标
- 将屏幕镜像到平板电脑
- 使用物理键盘发送键盘输入
- 硬件加速视频编码

上述功能在所有操作系统上都可用，但 Weylus 在 Linux 上效果最好。

Linux 上的其他功能包括：

- 支持手写笔 / 笔（支持压力和倾斜）
- 多点触控：尝试使用支持多点触控的软件，如 Krita
- 捕获特定窗口并仅绘制到它们
- 更快的屏幕镜像
- 平板电脑作为第二个屏幕

[https://github.com/H-M-H/Weylus](https://github.com/H-M-H/Weylus)

## ProjClean: 查找和清理 build 和 cache 目录

ProjClean 会为你找到 node_modules(node)、target(rust)、build(java)等目录及其存储空间，所以你可以很容易地检查或清理。

[https://github.com/sigoden/projclean](https://github.com/sigoden/projclean)

## Zine - 简单易用的个人杂志生成器

- 移动设备优先。
- 直观而优雅的杂志设计。
- 最佳的阅读体验。
- 主题可定制，扩展友好。
- 支持 RSS Feed 。
- 支持开放图谱协议（Open Graph Protocol）。
- 建立静态网站，托管在任何地方。

- [https://github.com/zineland/zine](https://github.com/zineland/zine)
- 《TO-D 杂志》- 由 Zine 驱动: [https://github.com/zineland/2d2d](https://github.com/zineland/2d2d)

## 跨平台文件搜索引擎：orange

[https://github.com/naaive/orange](https://github.com/naaive/orange)