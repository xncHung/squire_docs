# Navigation

Navigation works in both directions: from source to the graph, and from the graph back to source.

## Source → graph (gutter icons)

A Dagger icon appears in the editor gutter (Java and Kotlin) on:

- `@Component`, `@Subcomponent`, `@AssistedFactory`, `@HiltAndroidApp`, `@AndroidEntryPoint` classes
- Classes with `@Inject` fields (pure Dagger members-injection targets; Kotlin: `@Inject` properties, including `@field:Inject`)
- `@Component.Builder` / `@Component.Factory` / `@Subcomponent.Builder` / `@Subcomponent.Factory` nested interfaces
- `@Provides`, `@Binds`, `@BindsOptionalOf` methods
- `@Inject`, `@AssistedInject` constructors

Clicking it opens/activates the **Dagger Graph** tool window and centers the corresponding node at actual size (1:1) with a blink highlight.

Edge cases:

- If the node belongs to a `@Binds` delegate that is currently inlined (the **Inline** toggle), the click reveals the edge that passes through the delegate instead.
- If the node is not part of the currently selected root component's graph, a balloon notification says so ("Node not in the graph of the selected root component") — switch the component in the picker.

## Graph → source

**Click a node** to jump to its declaration:

- Component nodes → the component class.
- Creator nodes (`Builder` / `Factory`) → the creator interface itself, not the component.
- `@Provides` / `@Binds` / multibinding contributions / `@BindsOptionalOf` → the declaring module method.
- `@AssistedFactory` → the factory interface.
- Injection, members-injection, `@BindsInstance` and similar nodes → the key type's declaration.
- Inlined `@Binds` edges are clickable too: they jump to the first method of the delegation chain.
- Entry-point ports on component nodes → the declaring method, including methods inherited from super-interfaces.
- Synthesized collection nodes (MultiBoundSet/Map) have no source and are not clickable.

Navigable elements show a hand cursor on hover.

## Tips

- **Double-click an edge** to bounce between its two endpoints when tracing a long chain.
- Use **Locate** (toolbar) to jump to any node by name without leaving the canvas.
- In large graphs, combine the [floating window](toolwindow.md#toolbar) with your editor on another monitor: click-through navigation keeps working from the floating window.
