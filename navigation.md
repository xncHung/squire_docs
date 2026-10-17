# Navigation

Navigation works in both directions: from source to the graph, and from the graph back to source.

## Source → graph (gutter icons)

A Dagger icon appears in the editor gutter (Java and Kotlin) on:

- `@Component`, `@Subcomponent`, `@AssistedFactory`, `@HiltAndroidApp`, `@AndroidEntryPoint` classes
- Classes with `@Inject` fields (pure Dagger members-injection targets; Kotlin: `@Inject` properties, including `@field:Inject`)
- `@Component.Builder` / `@Component.Factory` / `@Subcomponent.Builder` / `@Subcomponent.Factory` nested interfaces
- `@Provides`, `@Binds`, `@BindsOptionalOf` methods
- `@Inject`, `@AssistedInject` constructors

Clicking it opens/activates the **Dagger Graph** tool window. With **Focus on navigation** on (default), it opens the node's focused in/out view; turn the option off to center the node in the full graph at actual size (1:1) with a blink highlight.

Edge cases:

- If the node belongs to a `@Binds` delegate that is currently inlined (the **Inline** toggle), the click jumps to the delegate's implementation node (the end of the delegation chain) instead; when several edges pass through the same delegate, they all lead to it. With inlining off, it jumps to the `@Binds` node.
- If the node is not part of the currently selected root component's graph, a balloon notification says so ("Node not in the graph of the selected root component") — switch the component in the picker.
- When a focused view is open, gutter navigation replaces it: with **Focus on navigation** on (default) it opens the newly hit node's focused view directly; with the option off it returns to the full graph first and then centers the node there.

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
