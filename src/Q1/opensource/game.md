# 游戏与图像处理

## vange-rs: 是对一款具有独特玩法和技术创新的传奇游戏Vange的Rust重新实现

该项目是由[wgpu-rs](https://github.com/gfx-rs/wgpu-rs)作者 kvark 实现的。

技术栈：

该游戏使用[wgpu-rs](https://github.com/gfx-rs/wgpu-rs)进行图形处理，使用[winit](https://github.com/tomaka/winit)进行窗口化。

关卡是在单个全屏绘制调用中绘制的，带有一点光线追踪魔法。还有一个实验性的基于曲面细分的渲染器，但都没有产生足够质量的结果。请参阅渲染技术的[专用 wiki 页面](https://github.com/kvark/vange-rs/wiki/Rendering-Techniques)。

注意：该项目依赖于原版游戏的素材，所以需要你自己在[GOG](https://www.gog.com/game/vangers)购买该游戏（28元）才能跑，而且不支持 macosx m1。

- [https://github.com/kvark/vange-rs](https://github.com/kvark/vange-rs)
- [https://vange.rs/](https://vange.rs/)