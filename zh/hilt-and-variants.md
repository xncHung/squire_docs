# Hilt、变体与多模块工程

> [English](../hilt-and-variants.md)

## Squire 如何看 Hilt

Hilt 的组件在编译期生成。Squire 索引 `build/generated/hilt/component_sources/<variant>/` 下生成的 `*_HiltComponents.java` 产物，并把它们当作根组件。你在模块里声明的绑定（`@Module`、`@AndroidEntryPoint` 成员注入等）从源码和库字节码解析（jar/aar 里的类，比如 Hilt 自带的系统模块，通过 ASM 索引）。

推论：

- **先至少跑过一次 KSP/KAPT（或 `annotationProcessor`）**再期待图出现。生成产物是组件列表的可信源——而且部分绑定本身就由 Hilt 处理器生成到产物源码里，没跑过处理器时图会丢这些绑定（显示为 `MISSING` 节点）。
- 测试变体（`*UnitTest`、`*AndroidTest`）默认隐藏。开启工具栏的**包含测试模块**后列出；它们的图在所属模块的测试类路径内解析。Hilt 定制测试组件在条目 tooltip 里标注归属的测试类；共享测试根标记为共享。
- 任何变体都没有构建产物时（比如刚 clone），暂时列不出 Hilt 组件。

## 构建变体

- 组件选择器给**当前构建变体**的组件标记 `*` 并预选。该变体的测试组件也会打星，但预选始终优先 production 组件而非测试组件。
- 变体探测是**按模块**的：app 模块和它包含的库各自跟随 IDE Build Variants 面板里选中的变体。
- 没有 Android 插件时（纯 IntelliJ IDEA），变体探测降级为最近修改的 `component_sources/<variant>` 目录。
- 从未为当前变体构建过的组件仍可能被列出（经 build 目录归属启发式）；构建该变体之前，它的图可能不完整。

## 多模块工程

跨模块、跨变体的同名组件是不同条目，在选择器里按变体、包名、模块后缀区分。图的解析限定在组件所属模块的运行时类路径和变体内，其他变体/模块的同名声明不会泄漏进图。

## 索引自愈

IDE 索引偶尔会漏掉生成的 Hilt 产物——最常发生在刚切换变体、同一生成文件路径在变体间翻转的时候。Squire 建图前会探测预期产物，对漏掉的强制补索引（毫秒级；日志会报 `repaired N unindexed hilt artifacts`）。

切换变体后图还是不对的话，见[故障排查](troubleshooting.md)。
