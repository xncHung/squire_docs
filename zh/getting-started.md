# 快速上手

> [English](../getting-started.md)

> **安装之后：** IDE 会执行一次全项目重建索引——Squire 维护自己的 Dagger 绑定索引，构建它需要一次全量扫描。**索引完成前工具窗口什么都不显示**（留意 IDE 的索引进度条）。每个项目只发生一次，之后的启动不受影响。

## 环境要求

- **IntelliJ IDEA 2025.2+** 或 **Android Studio**（平台版本 252–262），并启用 Kotlin 插件。
- Android 插件**可选**：纯 IntelliJ IDEA 也能用（此时构建变体探测降级为生成产物目录探测）。

## 纯 Dagger 项目：零配置

Squire 直接从 Java/Kotlin 源码索引 Dagger 绑定。不需要构建，不需要配置：

1. 打开 **Dagger Graph** 工具窗口（默认在右侧边栏）。
2. 从顶部下拉框选一个根 `@Component`。
3. 绑定图在后台解析，完成后渲染。

## Hilt 项目：先跑注解处理器

Hilt 的一部分接线在编译期生成。Squire 读取这些生成产物（`build/generated/hilt/component_sources/<variant>/`），所以：

1. **至少跑过一次 KSP/KAPT（或 `annotationProcessor`）**——通常构建一次项目即可。这有两个原因：组件入口点来自生成产物，**而且部分绑定本身只存在于 Hilt 处理器生成的源码里**。没跑过处理器时，这些绑定在图上显示为 `MISSING` 节点。
2. 打开 **Dagger Graph** 工具窗口，选择一个 `*_HiltComponents.*` 入口。
3. 如果你刚切换过构建变体，先把该变体构建一次——见 [Hilt、变体与多模块工程](hilt-and-variants.md)。

如果 IDE 索引还没来得及收录新生成的文件，Squire 会在建图前自动检测并补索引。如果首次构建完成时工具窗口正开着，它会在再次可见时自动刷新。

## 你应该看到什么

- 根组件是一个容器框，从它可达的所有绑定作为节点，按依赖层级从左到右排布。
- 解析过程中的进度遮罩（"Resolving dependency graph… n%"）。
- 红色的 `MISSING` 节点表示没有任何绑定能满足的被请求 key。

什么都没显示的话，见[故障排查](troubleshooting.md)。
