# Squire(侍剑) —— IDE 里的 Dagger/Hilt 依赖图

> [English](../index.md)

Squire把项目中任意根 `@Component` 的完整 Dagger/Hilt 绑定图，以可交互、可导航的图形式呈现在 IntelliJ IDEA 和 Android Studio 里。

![Squire Dagger Graph tool window](../assets/squire_dagger_graph_nia.png)

## 为什么用 Squire

Dagger 的接线在编译期是不可见的：绑定来自模块方法、`@Inject` 构造函数、组件依赖、多重绑定贡献，以及——在 Hilt 下——按构建变体生成的组件层级。当注入失败或注入了错误的实现时，你通常只能手工翻生成代码。Squire 解析出与编译器所见一致的图并画出来，让你几秒内回答"这个绑定从哪来？"和"谁依赖这个类型？"。

## 文档

> **刚装好 Squire？** IDE 会执行一次全项目重建索引来构建 Squire 的 Dagger 绑定索引，**索引完成前工具窗口保持空白**。这是预期行为——见[快速上手](getting-started.md)。

- [快速上手](getting-started.md) —— 环境要求、第一张图、Hilt 前置条件
- [工具窗口](toolwindow.md) —— 工具栏、开关、浮动窗口、进度、空状态
- [读懂图](reading-the-graph.md) —— 节点类型、边、端口、颜色、多重绑定
- [导航](navigation.md) —— 图 → 源码、行槽图标 → 图
- [Hilt、变体与多模块工程](hilt-and-variants.md)
- [故障排查](troubleshooting.md) —— 刷新 vs 强制刷新、图过期、索引问题
- [已知限制](limitations.md)
- [最终用户许可协议](eula.md)（中文译本仅供参考，以[英文版](../eula.md)为准）

## 问题反馈

在 [issue tracker](https://github.com/xncHung/squire_docs/issues) 提交 bug 和功能建议。反馈图相关问题时请附上：IDE 及版本、Squire 版本、项目是否使用 Hilt（KSP 还是 KAPT）、当前构建变体，以及图（或缺失部分）的截图。
