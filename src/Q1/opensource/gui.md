# 开源观察 ｜GUI

## egui 0.17.0 发布

本次发布的主要内容：

- Context menus
- Rich Text (colors, underline, ...) in all widgets
- Bar Charts and box plots

[https://github.com/emilk/egui/blob/master/CHANGELOG.md](https://github.com/emilk/egui/blob/master/CHANGELOG.md)

## Dioxus v0.1 发布

Dioxus 是一个新的 Rust GUI 工具包，用于 Web、桌面、移动、SSR、TUI，关注开发人员体验，有下面几点优势：

- 熟悉：提供类似 React 的心智模型和 API
- 健壮：借助 Rust，将规则和错误处理移入类型系统来避免运行时错误
- 高性能：可扩展到最大的应用程序
- 高效：全面的文档、快速重新编译和深度集成的工具
- 可扩展：适用于所有平台的可重用 hook 和组件

[https://dioxuslabs.com/blog/introducing-dioxus/](https://dioxuslabs.com/blog/introducing-dioxus/)

## SixtyFPS现已更名为Slint

我们将`SixtyFPS`改名为`Slint`。

**为什么要改变？**

在2020年春天，我们启动了SixtyFPS，旨在为桌面和嵌入式设备创建一个新的GUI框架。

在将近两年的时间里，我们已经发布了13个版本，赢得了许多用户，创建了一个贡献者社区，签署了一些客户，并获得了3k个GitHub star🤣。

我们选择SixtyFPS这个名字是因为我们想传达我们的目标：即始终实现流畅的动画，这通常与每秒60帧的刷新率有关。

许多旁观者从字面上理解这个名字。每当我们的项目出现在社交媒体上，我们就会收到关于我们名字的评论：考虑到现在的屏幕经常以更高的刷新率运行，这个名字显得过时了。

我们考虑改变这个名字，并决定在开放源码社区讨论这个问题，以收集意见。我们在GitHub上展开了讨论，并收到了许多好的建议。

[@karoofish](https://github.com/karoofish)提出了`slant`，这成为投票率最高的建议，我们非常喜欢它。我们和一些朋友讨论了这个问题，包括一些讲母语的人，有些人建议，在某些情况下，它可以有一点负面的意思。我们所需要的只是一个小小的迭代。

我们的新名字来自于**S**traightforward, **L**ightweight, **N**ative **T**oolkit，这也是我们的设计目标：`Slint`。

衷心感谢每一个参与其中的人，尤其是[@karoofish](https://github.com/karoofish)!

![img](https://slint-ui.com/resources/printerdemo_screenshot.png)

**什么是Slint？**

对于那些从未听说过~~SixtyFPS~~ **Slint**的人来说，它是一个用Rust编程语言实现的GUI工具箱。

其核心是声明性的Slint标记语言，它代表了整个用户界面。Slint语言从Qt的QML获得了一些灵感。这很自然，因为我们来自那个生态系统。我们决定从头开始建立一个强大的类型系统，一个设计师友好的单元系统和完整的工具支持。用Slint标记语言编写用户界面，用 "真正的" 编程语言编写业务逻辑。

你可以用不同的编程语言使用Slint，比如Rust、C++和JavaScript。我们为这些语言设计的API是直观和习惯性的，而不仅仅是一些自动生成的绑定(binding)。我们设计了这个工具包，以便我们可以将Python、Go或其他语言加入到这个组合中。

我们自己的编译器可以解析Slint语言，并直接编译成本地的Rust或C++代码，具有本地性能和高效的内存布局。你也可以用我们的运行时解释器在运行时动态地加载`.slint`文件。

我们的工具包括一个实现了语言服务器协议的语言服务器，可以方便的集成到各种IDE和编辑器中。它提供了自动完成、语义语法高亮和一个很酷的实时预览，正如下面的[视频片段](https://slint-ui.com/blog/sixtyfps-becomes-slint/preview_codelens.mp4)所演示的那样。

**Slint 0.2.0版本**

0.2.0版本的目的是为了反映这一名称的变化。

我们也借此机会对我们的API进行了一些重要的修改，这些修改是我们从去年0.1.0版本开始收集的，在此感谢你们的反馈。

下面是你如何升级你的应用程序：

- 确保你的编程环境符合我们更新的工具要求：Rust 1.56或更高版本。如果你使用的是C++，请使用支持C++ 20的编译器。
- 如果你使用的是Rust，把你的Cargo.toml改成依赖`slint`而不是 `sixtyfps`。如果你使用的是C++，修改你的CMakeLists.txt以找到Slint包，并针对Slint::Slint目标进行链接。
- 在你的应用程序代码中，用slint重命名所有出现的 sixtyfps 模块/命名空间。关于API的变化，请参阅我们的Rust（+解释器）和C++迁移指南。

在这个版本中，我们还释放了一个新的功能：你现在可以在Slint场景的渲染之前或之后集成自定义的OpenGL代码。新的[OpenGL Underlay Example](https://slint-ui.com/demos/opengl_underlay)展示了如何在Rust和C++中做到这一点。

![img](https://slint-ui.com/blog/sixtyfps-becomes-slint/opengl-underlay.png)

**新的网站和设计**

你可能已经注意到，我们网站的位置、设计和标志都发生了变化。在Benedikt Wisbauer的帮助下，我们利用这个机会更新了我们的品牌形象；我们还移动了GitHub上的组织。

你可以在这里找到我们新的GitHub仓库：[github.com/slint-ui/slint](https://github.com/slint-ui/slint)。

我们希望你能与我们的新名称、品牌和我们的最新版本联系起来。🚀❤️

如果你对我们的旧网站感到好奇，它仍然可以在互联网档案中找到。

[SixtyFPS becomes Slint — Slint Blog](https://slint-ui.com/blog/sixtyfps-becomes-slint.html)

## ImageSieve: 用于对图像进行排序和分类的GUI工具。

具体而言，将所有图像和视频放在一个文件夹中，用 ImageSieve 打开文件夹，将不值得保留的图像和视频标记为丢弃，定义时间段内的一些重大事件（如生日， 旅行或婚礼），选择一个目标文件夹，ImageSieve 将自动完成工作。

[https://github.com/Futsch1/image-sieve](https://github.com/Futsch1/image-sieve)

## Rust进行QT开发的新方式 CXX-Qt

这个库目前还未到产品级阶段，但是已经可用了。大家有机会可以尝试尝试。

[https://www.kdab.com/cxx-qt/](https://www.kdab.com/cxx-qt/)