# 导航

> [English](../navigation.md)

导航是双向的：从源码到图，从图回到源码。

## 源码 → 图（行槽图标）

编辑器行槽（Java 和 Kotlin）会在以下位置显示 Dagger 图标：

- `@Component`、`@Subcomponent`、`@AssistedFactory`、`@HiltAndroidApp`、`@AndroidEntryPoint` 类
- 含 `@Inject` 字段的类（纯 Dagger 的成员注入目标；Kotlin 为 `@Inject` 属性，含 `@field:Inject` 写法）
- `@Component.Builder` / `@Component.Factory` / `@Subcomponent.Builder` / `@Subcomponent.Factory` 嵌套接口
- `@Provides`、`@Binds`、`@BindsOptionalOf` 方法
- `@Inject`、`@AssistedInject` 构造函数

点击它会打开/激活 **Dagger Graph** 工具窗口，把对应节点以实际大小（1:1）居中并闪烁高亮。

边角情况：

- 如果节点属于当前被内联的 `@Binds` 委托（**内联**开关），点击改为定位穿过该委托的边。
- 如果节点不在当前选中根组件的图里，会弹出气球通知（"Node not in the graph of the selected root component"）——在选择器里切换组件。

## 图 → 源码

**点击节点**跳转到声明处：

- 组件节点 → 组件类。
- 构建器节点（`Builder` / `Factory`）→ 构建器接口本身，而不是组件。
- `@Provides` / `@Binds` / 多重绑定贡献 / `@BindsOptionalOf` → 声明它的模块方法。
- `@AssistedFactory` → 工厂接口。
- Injection、成员注入、`@BindsInstance` 等节点 → key 类型的声明处。
- 内联的 `@Binds` 边也可点击：跳到委托链的第一个方法。
- 组件节点上的入口点端口 → 声明该方法的接口/抽象类中的方法，含继承自父类型的方法。
- 合成集合节点（MultiBoundSet/Map）没有源码，不可点击。

可导航的元素悬停时显示手型光标。

## 技巧

- **双击边**可以在两个端点间往返，适合追长链。
- 用工具栏的**定位**按名字跳到任意节点，不用离开画布。
- 大图中可以把[浮动窗口](toolwindow.md#工具栏)拖到另一台显示器配合编辑器使用：浮动窗口里的点击导航照常工作。
