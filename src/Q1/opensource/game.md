# 游戏与图像处理


## Bevy 0.6 发布

Bevy 目前还未稳定，所以版本更新会出现 break change的情况。升级需要看[0.5 到 0.6 迁移指南](https://bevyengine.org/learn/book/migration-guides/0.5-0.6/)。

0.6 版本改进：

- 一个全新的现代渲染器，更漂亮、更快速、更容易扩展
- 方向性和点状光影
- 集群式向前渲染
- 锥体裁剪剔除
- 以更少的模板大幅提高精灵的渲染速度
- 本地WebGL2支持。你可以在你的浏览器中运行Bevy的例子来测试这一点
- 更方便地自定义材质
- 更强大的着色器：预处理器、导入、WGSL支持
- Bevy ECS的人机工程学和性能改进。不再有`.system()`!
- 健全性和正确性的改正。

**重点摘要：ECS 改进**

Bevy ECS 的最优先准则是 人体工程学（ergonomics），即，开发体验要符合人类的习惯。Bevy 开发者们花了大量的精力来做到这一点。

1. 消除 system 方法调用

这是 0.5 版本增加 System 的示例：

```rust,ignore
// This is a standalone Bevy 0.5 App that adds a simple `gravity` system to the App's schedule
// and automatically runs it in parallel with other systems
fn main() {
    App::build()
        .add_plugins(DefaultPlugins)
        .add_system(gravity.system())
        .run();
}

fn gravity(time: Res<Time>, mut query: Query<&mut Transform>) {
    for mut transform in query.iter_mut() {
        transform.translation.y += -9.8 * time.delta_seconds();
    }
}
```

这是0.6的改进，完全去掉了 `.system()`。

```rust,ignore
// pure bliss!
App::new()
    .add_plugins(DefaultPlugins)
    .add_system(gravity) // 不需要调用 .system()
    .run();
```

2. 改进组件trait和增加`#[derive(Component)]`

在Bevy 0.6中，类型不再默认实现 `Component` trait。在过去的版本中，通过下面这样一揽子（blanket impl）的实现为所有类型实现了 `Compont` trait 。

```rust,ignore
impl<T: Send + Sync + 'static> Component for T {}
```

但是在对Bevy ECS有深刻理解及对未来计划有很大变化之后，发现这样的设计存在以下问题：

- 不是所有的东西都应该是组件。新用户不小心将捆绑类型和类型构造器添加为组件是在Bevy Discord上最常见的`#help channel threads`，这类错误是很难调试的。
- 如果我们对所有东西都自动实现了Component，我们就不能用相关的类型来定制Component类型。这就阻止了整类的优化。例如，Bevy ECS现在有多种Component存储类型。通过将存储类型移到Component中，我们使rustc能够优化通常需要在运行时进行的检查。
- 自动注册。将更多的逻辑转移到Component中，也使我们有能力在未来做更多的事情，比如 "在派生Component时自动注册反射"。
- 其他等，官网查看细节。

现在可以通过派生宏自动实现，也可以手动实现 `Component`。

```rust,ignore
// defaults to "Table" storage
#[derive(Component)]
struct SomeComponent;

// overrides the default storage
#[derive(Component)]
#[component(storage = "SparseSet")]
struct SomeComponent;
```

[更多细节： https://bevyengine.org/news/bevy-0-6/](https://bevyengine.org/news/bevy-0-6/)



## vange-rs: 是对一款具有独特玩法和技术创新的传奇游戏Vange的Rust重新实现

该项目是由[wgpu-rs](https://github.com/gfx-rs/wgpu-rs)作者 kvark 实现的。

技术栈：

该游戏使用[wgpu-rs](https://github.com/gfx-rs/wgpu-rs)进行图形处理，使用[winit](https://github.com/tomaka/winit)进行窗口化。

关卡是在单个全屏绘制调用中绘制的，带有一点光线追踪魔法。还有一个实验性的基于曲面细分的渲染器，但都没有产生足够质量的结果。请参阅渲染技术的[专用 wiki 页面](https://github.com/kvark/vange-rs/wiki/Rendering-Techniques)。

注意：该项目依赖于原版游戏的素材，所以需要你自己在[GOG](https://www.gog.com/game/vangers)购买该游戏（28元）才能跑，而且不支持 macosx m1。

- [https://github.com/kvark/vange-rs](https://github.com/kvark/vange-rs)
- [https://vange.rs/](https://vange.rs/)

## Bevy 渐变动画插件发布新版本

[https://github.com/djeedai/bevy_tweening](https://github.com/djeedai/bevy_tweening)