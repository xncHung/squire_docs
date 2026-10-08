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

依赖请求锚定在请求方节点右缘的**端口行**上——UML 字段风格的行，如 `repo: Repo` 或 `injectMembers(LoginActivity)`。端口精确告诉你是*哪个*参数或入口点发起了这个依赖请求。入边连接到目标节点的左缘。构建器节点（`@Component.Builder/Factory`、`@Subcomponent.Builder/Factory`）把 `build`/`create` 方法画为端口，包括从父接口继承的方法。

## 边

- **依赖边**（带箭头的圆角折线）：一次依赖请求，含组件入口点。
- **Set/Map 贡献边**：从合成集合节点指向每个贡献。内置 map key 显示在边旁的虚线注释框里。
- **子组件构建器边**：父组件 → 子组件。
- **内联的 `@Binds` 链**：改道的边用双尖括号（»）标记。
- **边 tooltip**：悬停边只显示完全在屏幕外的端点，并保留方向箭头（`→ X` / `X →`）——两端都有部分可见则不提示。经内联 `@Binds` 改道的边改为展开隐藏链：接口 → 实现。可用工具栏的**边预览**开关关闭。

## 交互

- **悬停**节点或边：高亮其所有相连边的完整路径。
- **点击**节点：导航到源码（见[导航](navigation.md)）。
- **双击边**：在两个端点之间交替定位（优先显示屏幕外的那个），并闪烁高亮。

## 主题

图跟随 IDE 的浅色/深色主题，包括高亮颜色。
