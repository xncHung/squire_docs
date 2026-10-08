# Reading the graph

## Layout

The graph is laid out with the ELK layered algorithm, left to right in dependency layers. Components are drawn as nested rounded containers (dashed borders, alternating background shades, name in the header); subcomponents appear inside their parent component, and edges route across container boundaries naturally.

## Nodes

Each node shows a UML-stereotype-style label: scope/qualifier annotations on top (rendered as in source, e.g. `@Singleton`, `@Named("baseUrl")`), and the binding key type below with generic arguments, arrays and wildcards faithfully rendered (`List<Foo>`, `? extends Repo`).

A **type badge** (letter pill) is pinned to the left of the title of binding nodes — the letter is the primary signal (color-weakness safe), the uniform blue is decorative. Hovering the badge shows the annotation name. Components, multibinding collections, optionals and missing bindings keep their fill colors and get no badge.

| Badge | Meaning |
|---|---|
| `P` | `@Provides` method (including set/map contributions declared with `@Provides`) |
| `B` | `@Binds` (including set/map contributions declared with `@Binds`) |
| `I` | `@Inject` constructor binding |
| `A` | `@AssistedInject` binding |
| `AF` | `@AssistedFactory` |
| `BI` | `@BindsInstance` |
| `MI` | Members injection (`injectMembers(...)` target / `MembersInjector`) |
| `SC` | Subcomponent creator (`@Subcomponent.Builder` / `.Factory`) |
| `CD` | Component dependency / its provision bindings |

| Node | Meaning |
|---|---|
| Component (blue) | A `@Component` / `@Subcomponent`. Root of (a part of) the graph. |
| Injection | `@Inject` constructor binding for the key type. |
| Provision | `@Provides` method binding. |
| Delegate | `@Binds` binding. Hidden by default when the **Inline** toggle is on. |
| BoundInstance | `@BindsInstance` value supplied at component construction. |
| ComponentDependency / ComponentProvision | A component dependency itself, and bindings exposed by its provision methods. |
| AssistInjection / AssistFactory | `@AssistedInject` type and its `@AssistedFactory`. |
| MembersInjection | Members-injection (`injectMembers(...)`) target. |
| SubcomponentCreator | `@Subcomponent.Builder` / `@Subcomponent.Factory`. |
| MultiBoundSet / MultiBoundMap (green) | Synthesized collection nodes for set/map multibindings. |
| Optional (yellow) | `@BindsOptionalOf` bindings (`java.util.Optional` and Guava `Optional`). |
| MISSING (red) | A requested key that no binding can satisfy. For optionals whose underlying type is legitimately absent, the red marker is expected and harmless. |

## Ports

Dependency requests anchor on **port rows** on the requesting node's right edge — UML-field-style rows like `repo: Repo` or `injectMembers(LoginActivity)`. The port tells you exactly *which* parameter or entry point requested the dependency. Incoming edges attach to the target's left edge. Creator nodes (`@Component.Builder/Factory`, `@Subcomponent.Builder/Factory`) draw their `build`/`create` methods as ports, including methods inherited from super-interfaces.

## Edges

- **Dependency edges** (rounded polylines with arrowheads): a dependency request, including component entry points.
- **Set/Map contribution edges**: from the synthesized collection node to each contribution. Built-in map keys are shown in a dashed note box next to the edge.
- **Subcomponent-creator edges**: parent component → subcomponent.
- **Inlined `@Binds` chains**: marked with a double chevron (») on the rerouted edge.
- **Edge tooltips**: hovering an edge shows only its completely off-screen endpoint(s), with a direction arrow (`→ X` / `X →`) — both endpoints at least partially visible means no tooltip. For edges rerouted through inlined `@Binds` delegates, the tooltip expands the hidden chain instead: interface → implementation. Disable via the toolbar's **Edge preview** toggle.

## Interaction

- **Hover** a node or edge: the full route of all its connected edges is highlighted.
- **Click** a node: navigate to source (see [Navigation](navigation.md)).
- **Double-click an edge**: alternates between revealing the source and target endpoints (whichever is off-screen first), with a blink highlight.

## Themes

The graph follows the IDE's light/dark theme, including highlight colors.
