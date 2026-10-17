# 读懂图

> [English](../reading-the-graph.md)

## 布局

图使用 ELK 分层布局算法，按依赖层级从左到右排布。组件画成嵌套的圆角容器（虚线边框、交替背景色、标题栏显示名字）；子组件出现在父组件内部，边自然地跨越容器边界路由。

## 节点

每个节点显示 UML 构造型风格的标签：顶部是作用域/限定符注解（按源码原样渲染，如 `@Singleton`、`@Named("baseUrl")`），下方是绑定 key 类型，泛型参数、数组和通配符都忠实呈现（`List<Foo>`、`? extends Repo`）。

绑定节点的标题左侧钉有一个**类型徽章**（字母 pill）——字母是一级信号（色弱安全），统一的蓝色仅作装饰。悬停徽章显示对应的注解名。组件、多重绑定集合、optional 和缺失绑定维持填充色表达，不配徽章。

| 徽章 | 含义 |
|---|---|
| `P` | `@Provides` 方法（含以 `@Provides` 声明的 set/map 贡献） |
| `B` | `@Binds`（含以 `@Binds` 声明的 set/map 贡献） |
| `I` | `@Inject` 构造函数绑定 |
| `A` | `@AssistedInject` 绑定 |
| `AF` | `@AssistedFactory` |
| `BI` | `@BindsInstance` |
| `MI` | 成员注入（`injectMembers(...)` 目标 / `MembersInjector`） |
| `SC` | 子组件构建器（`@Subcomponent.Builder` / `.Factory`） |
| `CD` | 组件依赖 / 其 provision 绑定 |

| 节点 | 含义 |
|---|---|
| Component（蓝） | `@Component` / `@Subcomponent`。图（的一部分）的根。 |
| Injection | key 类型的 `@Inject` 构造函数绑定。 |
| Provision | `@Provides` 方法绑定。 |
| Delegate | `@Binds` 绑定。**内联**开关打开时默认隐藏。 |
| BoundInstance | 组件构造时经 `@BindsInstance` 提供的值。 |
| ComponentDependency / ComponentProvision | 组件依赖本身，以及它的 provision 方法暴露的绑定。 |
| AssistInjection / AssistFactory | `@AssistedInject` 类型及其 `@AssistedFactory`。 |
| MembersInjection | 成员注入（`injectMembers(...)`）目标。 |
| SubcomponentCreator | `@Subcomponent.Builder` / `@Subcomponent.Factory`。 |
| MultiBoundSet / MultiBoundMap（绿） | set/map 多重绑定的合成集合节点。 |
| Optional（黄） | `@BindsOptionalOf` 绑定（`java.util.Optional` 和 Guava `Optional`）。 |
| MISSING（红） | 没有任何绑定能满足的被请求 key。对于底层类型合法缺省的 optional，红色标记是预期的、无害的。 |

## 端口

依赖请求锚定在请求方节点右缘的**端口行**上——UML 字段风格的行，如 `repo: Repo` 或 `injectMembers(LoginActivity)`。端口精确告诉你是*哪个*参数或入口点发起了这个依赖请求。入边连接到目标节点的左缘。构建器节点（`@Component.Builder/Factory`、`@Subcomponent.Builder/Factory`）把 `build`/`create` 方法画为端口，包括从父接口继承的方法。行序可通过工具窗口 **Options 菜单**的**端口序**配置：**允许重排**（默认）让布局引擎重排端口以减少交叉，**声明序**保持源码顺序。

## 边

- **依赖边**（带箭头的圆角折线）：一次依赖请求，含组件入口点。
- **Set/Map 贡献边**：从合成集合节点指向每个贡献。内置 map key 显示在边旁的虚线注释框里。
- **子组件构建器边**：父组件 → 子组件。
- **内联的 `@Binds` 链**：改道的边用双尖括号（»）标记。
- **边 tooltip**：悬停边只显示完全在屏幕外的端点，并保留方向箭头（`→ X` / `X →`）——两端都有部分可见则不提示。经内联 `@Binds` 改道的边改为展开隐藏链：接口 → 实现。可用工具窗口 **Options 菜单**的 **Tooltip → 边预览**关闭。
- **组件 tooltip**：悬停组件容器空白处显示该组件的路径。可用 **Tooltip → 组件 tooltip** 关闭。
- **所有路径**：底部面板列出根组件到该节点的全部简单路径（最短优先，上限 200 条并显示截断提示），搜索过程中流式追加；条目带 1 基编号，条目之间有一条细的主题色分隔线。拖动面板顶部的拖拽手柄可调整高度。选中一条路径后其边以琥珀色永久高亮，悬停高亮保持蓝色。关闭面板、隐藏工具窗口或重建图都会取消搜索并清除高亮。悬停节点本身不再显示 tooltip。
- **聚焦视图**：在任意节点（包括根组件节点）上右键，选择 **只显示我的出入边**，会在新的 1:1 画布上只展示该节点的可见入边和出边。工具栏左上角的 **← 返回全图** 按钮（或 **Esc**）会立即返回并恢复原来的视图。聚焦期间节点右键菜单和“悬停节点联动高亮出入边”关闭，边悬停高亮和其他交互仍然可用。**导航后聚焦** 开启（默认）时，从源码跳图会直接打开命中节点的这个视图。

## 交互

- **悬停**节点或边：高亮其所有相连边的完整路径。聚焦视图里悬停命中节点本身不再联动高亮——所有可见边本来就与它相连；悬停边仍然高亮该边。
- **点击**节点：导航到源码（见[导航](navigation.md)）。
- **双击边**：在两个端点之间交替定位（优先显示屏幕外的那个），并闪烁高亮。
- **右键**节点：打开节点菜单（非根节点的 **显示我的所有路径**，任意节点的 **只显示我的出入边**）。

## 主题

图跟随 IDE 的浅色/深色主题，包括高亮颜色。
