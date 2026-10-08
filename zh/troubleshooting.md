# 故障排查

> [English](../troubleshooting.md)

## 刷新 vs 强制刷新 vs 失效缓存

三个升级级别，从最轻的开始：

1. **刷新**（工具栏）——只重建当前组件的图。改完代码后用：绑定相关的 PSI 变更通常会自动失效缓存，普通刷新即可拾取。
2. **强制刷新**（工具栏）——丢弃所有图缓存，探测并补索引漏掉的 Hilt 产物，重新枚举根组件。切换构建变体后、Gradle sync 后，或选择器里缺了预期组件时用。
3. **Tools → Invalidate Caches and Rebuild Dagger Index**——核弹级选项：请求全量重建 Squire 的 Dagger 索引（IDE 级重索引，即一段 dumb 期），丢弃所有图缓存，索引就绪后自动刷新。刷新和强制刷新都修不好的图错误（如插件热更新后的索引漂移/损坏）时使用。

## 常见症状

**"No Dagger @Component found in project"**
- **刚装好插件**：IDE 正在执行一次性重建索引来构建 Squire 的绑定索引，完成前工具窗口什么都不显示。等索引进度条走完——这是预期行为，每个项目只发生一次。
- 从未构建过的 Hilt 项目：先跑 KSP/KAPT（或构建），然后重新打开工具窗口（空白状态下重新可见时会自动刷新）。
- 纯 Dagger 项目：确认组件在 **production** 源码里——测试源码不参与解析。

**你认为存在但显示缺失的绑定（`MISSING` 节点）**
- Hilt 项目：确认 KSP/KAPT（或 `annotationProcessor`）确实为**当前**变体跑过——部分绑定只存在于 Hilt 处理器生成的源码里，未构建的变体即使组件能列出来也会产生 `MISSING` 节点。
- 切换变体或 sync 之后 IDE 索引可能滞后：试试**强制刷新**。
- 如果问题是**插件更新**后立刻出现的：先重启 IDE（热更新的插件可能让索引处于半失效状态）；仍不恢复时执行 **Tools → Invalidate Caches and Rebuild Dagger Index**。
- 查一下[已知限制](limitations.md)：该绑定可能用了 Squire 不建模的构造（如 Dagger Producers）。

**图没有反映代码变更**
- 用**刷新**。Squire 跟踪 PSI 依赖并自动失效，但生成代码的变更（新的 KSP/KAPT 输出）有时需要强制刷新。

**点行槽图标提示 "Node not in the graph of the selected root component"（气球通知）**
- 该节点属于另一个根组件（或另一个变体的组件）。在组件选择器里切换。

**构建后组件列表为空或过期**
- 开关一次工具窗口，或按**强制刷新**——选择器会从生成产物重新枚举组件。

## 报 bug 时请附上的诊断信息

- IDE 及版本，Squire 版本。
- Hilt 还是纯 Dagger；KSP 还是 KAPT；当前构建变体。
- 故障时间段的 IDE 日志摘录（`Help → Show Log`），特别是 `DaggerComponentService` / `DaggerGraphPanel` 的行。
- 图或缺失部分的截图。
