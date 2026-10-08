# 已知限制

> [English](../limitations.md)

这些限制来自 Dagger 语义或 Squire 静态模型当前的边界。它们是有意的呈现，不是 bug——但如果某条挡住了你，欢迎提 issue。

## Dagger 语义

- **不支持 Dagger Producers**（`@Produces`、`@ProductionComponent`、`@ProducerModule`）。
- **仅 `javax.inject`。** `jakarta.inject` 注解不索引（导航能部分解析，但不出现在图里）。
- **`@AssistedInject` / `@AssistedFactory`**：不支持限定符，作用域注解无效——与 Dagger 自身语义一致，辅助注入的请求只面向工厂类型。
- **不支持 `? super T` / Kotlin `in T` 通配符**（Dagger 本身也拒绝把它们作为绑定 key）；降级为擦除后的原始类型。
- **自定义 `@MapKey` 值**（`@StringKey` / `@IntKey` / `@LongKey` / `@ClassKey` / `@LazyClassKey` 之外的）不标注在贡献边上。
- **声明处带泛型的多重绑定声明**不建模。
- **来自泛型方法或泛型声明类的绑定**在字节码索引的库中降级为擦除后的原始类型。

## 解析范围

- **默认排除测试源码**。开启工具栏的**包含测试模块**后，unit-test / instrumented-test 组件会列出并建图。production 图永远不会看到测试绑定。
- **`@BindValue` 字段不作为声明被索引**——字段上没有行槽图标。但 Hilt 为它们生成的绑定会以普通 provide 的形式出现在图里，导航落在生成的 `<Test>_BindValueModule.provides<Field>` 方法上（可以从方法体继续跳到测试字段）。`@TestInstallIn` 模块正常建模。
- **unit 与 instrumented 测试同模块时 classpath 混合**。测试组件在所属模块的测试类路径内解析。当 IDE 把每个 Gradle source set 建模为独立模块（Gradle/AGP 默认）时，unit-test 与 instrumented-test 源码互不混淆；若工程把两者放在同一模块，两条测试类路径会合并，只存在于其中一条的绑定可能出现在另一条的图里。
- **从未构建过的 Hilt 变体**无法贡献其生成产物；组件仍可能被列出（经 build 目录启发式），但图可能不完整——尤其是只存在于 Hilt 处理器生成源码里的绑定，在该变体跑过 KSP/KAPT 之前会缺失。
- 图在所属模块的**运行时类路径**内解析（模块自身、传递依赖、库和 SDK）——与 Dagger 生成代码实际运行的位置和 Hilt 构建期聚合的收集范围一致。`compileOnly`（由运行环境供给）依赖包含在内：它们是合法的 Dagger 绑定，运行时由环境提供（如定制 ROM/JRE）。该类路径之外模块里的绑定不出现，这也正是兄弟模块/兄弟 app 模块的同包同名类不会互相泄漏进图的原因。
- **非选中变体不被索引。** 你可以查看并为任意变体的组件建图，但 IDE 只索引当前选中的构建变体。Hilt 生成组件按所选根的变体读取，而模块方法与 `@Inject` 构造器按当前变体解析——非选中变体的类无法通过类查找命中（只能按文件查找）。当两个变体提供不同的同名实现时，图 → 源码导航可能落到当前变体的声明上。选中非当前变体的组件时，图上方会给出提示。我们**有意不做**变体感知解析：AGP 的源集匹配规则（`demoDebug` 的声明可能来自 `demo`/`debug`/`demoDebug`）叠加 IDE 导航限制，成本与收益不成比例。

## 呈现

- 节点标签使用短类型名；不同包的同名类型在标签里不做区分（导航目标仍然精确）。
- 过长的标签行按单行渲染（节点宽度自适应内容）；病态长行（超过 100 字符）以省略号截断。悬停/导航不受影响。
- 行槽图标（源码 → 图）不出现在 `build/generated/hilt/component_sources/` 下的 Hilt 生成文件里——IDE 不把这些被排除的目录当作源码，不计算行标记。图 → 源码导航进入这些文件仍然可用。
- **`@BindsOptionalOf` 在一张图里可能出现为多个节点**——有些是 `MISSING`，有些实际有绑定（按请求点状态各一）。从行槽图标导航到图时，命中其中任意一个。正经的修法（多个节点命中时弹出选择列表）实现成本高、收益小，按现状接受。
