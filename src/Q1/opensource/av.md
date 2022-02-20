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