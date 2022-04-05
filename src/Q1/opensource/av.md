# 音视频处理

---

## gyroflow: 可以根据来自陀螺仪和可选的加速度计的运动数据来稳定视频

现代相机在内部记录这些数据（GoPro、Sony、Insta360 等），并且此应用程序通过使用它们来精确稳定捕获的镜头。它还可以使用来自外部源（例如来自 Betaflight blackbox）的陀螺仪数据。

用它可以省下买大疆手持稳定器的钱？

支持的陀螺源：

- GoPro（HERO 5 及更高版本）
- 索尼（a1、a6600、a7c、a7r IV、a7 IV、a7s III、a9 II、FX3、FX6、RX0 II、RX100 VII、ZV1、ZV-E10）
- Insta360 (OneR, SMO 4k, GO2)
- Betaflight 黑盒（CSV 和二进制）
- 移动应用程序：Sensor Logger, G-Field Recorder,Gyro
- Runcam CSV（Runcam 5 橙色，iFlight GOCam GR）
- WitMotion（WT901SDCL 二进制文件和 *.txt）

支持Windows 10 64 位（1809 或更高版本）/ macOS 10.14 或更高版本（本机支持 Intel 和 Apple Silicon）/ Linux / 安卓9 等，具体请参考该项目仓库README文档。

技术栈：FFmpeg/QT/Wgpu/OpenGL/OpenCV

- [官网](https://gyroflow.xyz/)
- [https://github.com/gyroflow/gyroflow](https://github.com/gyroflow/gyroflow)


## Kira 一个用于游戏开发的音频库

Kira 是一个跨平台库，用于为游戏创建富有表现力的音频。它提供了用于平滑调整声音属性的参数、用于将效果应用于音频的灵活混音器以及用于精确定时音频事件的时钟系统。

[github.com/tesselode/kira](https://github.com/tesselode/kira)

## mmids: 多媒体摄取与分发系统

Mmids 是一个功能强大、用户友好、开源的实时视频工作流服务器。


- [https://kalldrexx.github.io/mmids/](https://kalldrexx.github.io/mmids/)
- [https://github.com/KallDrexx/mmids/](https://github.com/KallDrexx/mmids/)

## OctaSine 0.6.0 发布：修复了主要的bug和一些其他变化

OctaSine 是用 Rust 编写的 VST2 频率调制合成器。

以前的版本有两个问题需要解决。一个是小型音频缓冲区处理不当，还有一个是笔记与样本的时间安排不正确。因为修复这些问题意味着改变音频输出，所以作者决定在同一版本中也做其他影响声音生成的更改和修复。

[https://github.com/greatest-ape/OctaSine](https://github.com/greatest-ape/OctaSine)

## GStreamer Rust bindings 0.18.0 发布了

GStreamer 是一个音视频框架库, gstreamer-rs 是他的 Rust binding, 目前 0.18.0 发布了.

[https://gstreamer.freedesktop.org/news/#2022-01-16T11:00:00Z](https://gstreamer.freedesktop.org/news/#2022-01-16T11:00:00Z)

## fundsp：音频DSP库

FunDSP 是一个专注于可用性的音频 DSP（数字信号处理）库。

可用于：

- 游戏和应用程序的音频处理和合成
- 教育
- 音乐制作
- 声音 hacker 和音频 golfing
- DSP 算法原型

[https://github.com/SamiPerttu/fundsp](https://github.com/SamiPerttu/fundsp)

## Symphonia 发布 v0.5

Symphonia 是一个音频解码和媒体解复用库，支持 AAC、ALAC、FLAC、MKV、MP3、MP4、OGG、Vorbis、WAV 和 WebM 多种格式。类似 FFMpeg，不过是用纯 Rust 来实现的。

[https://github.com/pdeljanov/Symphonia](https://github.com/pdeljanov/Symphonia)

## gyroflow: 可以根据来自陀螺仪和可选的加速度计的运动数据来稳定视频

现代相机在内部记录这些数据（GoPro、Sony、Insta360 等），并且此应用程序通过使用它们来精确稳定捕获的镜头。它还可以使用来自外部源（例如来自 Betaflight blackbox）的陀螺仪数据。

用它可以省下买大疆手持稳定器的钱？

支持的陀螺源：

- GoPro（HERO 5 及更高版本）
- 索尼（a1、a6600、a7c、a7r IV、a7 IV、a7s III、a9 II、FX3、FX6、RX0 II、RX100 VII、ZV1、ZV-E10）
- Insta360 (OneR, SMO 4k, GO2)
- Betaflight 黑盒（CSV 和二进制）
- 移动应用程序：Sensor Logger, G-Field Recorder,Gyro
- Runcam CSV（Runcam 5 橙色，iFlight GOCam GR）
- WitMotion（WT901SDCL 二进制文件和 *.txt）

支持Windows 10 64 位（1809 或更高版本）/ macOS 10.14 或更高版本（本机支持 Intel 和 Apple Silicon）/ Linux / 安卓9 等，具体请参考该项目仓库README文档。

技术栈：FFmpeg/QT/Wgpu/OpenGL/OpenCV

- [官网](https://gyroflow.xyz/)
- [https://github.com/gyroflow/gyroflow](https://github.com/gyroflow/gyroflow)