# 移动开发领域

## cargo-mobile ：提升 Rust 在移动开发领域体验的便利工具

cargo-mobile 是一个与框架无关的工具，可以生成 Rust 移动项目的所有样板文件，并且包含大量用于在移动设备上构建和运行的便捷命令。

目前仅支持 macOS 和 Linux 。

[https://github.com/BrainiumLLC/cargo-mobile](https://github.com/BrainiumLLC/cargo-mobile)

## cargo-xcodebuild: 编译和运行 iOS 的 apps

让 cargo 编译和运行 iOS 的 apps.

例如, 只要很小的改动,就可以使用 `xcodebuild` 来运行 `bevy/miniquad/wgpu/etc..`

```toml
[lib]
crate-type = ["staticlib"]

[package.metadata.ios]
build_targets = ["aarch64-apple-ios", "aarch64-apple-ios-sim", "x86_64-apple-ios"]
```
在 `lib.rs` 中
```rust
#[no_mangle]
pub extern "C" fn main_rs() {
    // start game code here
}
```

运行步骤:

- 启动一个模拟器
- `cargo xcodebuild run`

[https://github.com/Gordon-F/cargo-xcodebuild](https://github.com/Gordon-F/cargo-xcodebuild)

## swift-bridge 促进 Rust 和 Swift 互操作

可以在 Rust 和 Swift 之间传递和共享高级类型，例如 Option<T>、String、Struct 和 Class。

- [https://chinedufn.github.io/swift-bridge/](https://chinedufn.github.io/swift-bridge/)
- [https://github.com/chinedufn/swift-bridge](https://github.com/chinedufn/swift-bridge)

## flutter_rust_bridge  一个用于 Flutter/Dart 与 Rust 的高级内存安全绑定生成器

它开源将 Flutter（一种跨平台的热重载快速开发 UI 工具包）和 Rust（一种使每个人都能够构建可靠且高效的软件的语言）结合起来。

[https://github.com/fzyzcjy/flutter_rust_bridge](https://github.com/fzyzcjy/flutter_rust_bridge)

## rust_android_ios寻找维护者

项目通过使用共享库来防止代码重复，保持完全原生的 UI 体验和对平台最新 API 的简单访问。它也非常灵活，允许在不同平台之间轻松迁移，包括传统的跨平台框架，如 Flutter 或 React Native。例如，您可以使用 Rust+React Native 或 Rust+Flutter 开发您的 MVP，然后迁移到原生 iOS/Android，而无需重写所有内容。 您甚至可以使用 WebAssembly 或桌面应用程序将您的核心重用于 Web 应用程序（同样，您可以使用本机或跨平台框架，如 Electron）。

如果你有意向，可以在项目上开个 Issue，或给作者发邮件：mailto:ivanhp978@gmail.com

[https://github.com/ivanschuetz/rust_android_ios](https://github.com/ivanschuetz/rust_android_ios)