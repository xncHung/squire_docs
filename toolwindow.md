# The tool window

The **Dagger Graph** tool window (right sidebar) hosts the graph canvas, a component picker, and a toolbar.

## Component picker

Two drop-downs: the **module bucket** first, then the **component** inside it. The list covers every root `@Component` in the project: source components (pure Dagger) and Hilt-generated components. `@Subcomponent`s are not listed — they appear inside their parent's graph.

- Both popups are the platform's built-in chooser: they filter as you type (camel-case and substring), and long module names are shown with the common package prefix stripped.
- Each component entry carries a tooltip describing its ownership (e.g. the test class that customizes a Hilt test component).
- The entry matching the **current build variant** is marked with `*`. The marker updates when you change the variant in the IDE's Build Variants panel (the picker refreshes each time it opens).
- Same-named components are disambiguated with a variant suffix (Hilt multi-variant projects), then a package suffix, then a module suffix.
- In Android projects, each module's own selected variant is respected: an app module and a library module can track different variants.
- When the selected component belongs to a **different variant than the IDE's current one**, a dismissible banner at the bottom of the canvas says so — source navigation follows the current variant (see [Known limitations](limitations.md)).

## Toolbar

| Button | What it does |
|---|---|
| **Refresh** | Invalidates the cached graph for the current component and reloads it. |
| **Zoom In / Zoom Out** | Zooms around the viewport center (1.2× steps). Also: Ctrl/Cmd + mouse wheel, or trackpad pinch. |
| **Fit to Window** | Scales the whole graph into the viewport and re-enables auto-fit on resize. |
| **Actual Size (1:1)** | Zooms to screen pixels. |
| **Locate** | Opens a quick-search popup over all nodes of the current graph. CamelCase humps, substring and case-insensitive matching all work; components sort first. Enter/double-click centers the node and blinks it. |
| **Inline** (toggle, default on) | Inlines `@Binds` delegates: pure delegation bindings are not drawn as nodes; edges route straight to the final provider and are marked with a double chevron (»). The state is persisted per project. |
| **Include test modules** (toggle, default off) | Also lists unit-test / instrumented-test components; their graphs resolve against the owning module's test classpath. Persisted per project. |
| **Wrap** (toggle, default off) | Cuts extremely wide graphs into stacked rows (target aspect ratio ≈ 1.6). Small graphs are never wrapped. Persisted per project. |
| **Edge preview** (toggle, default on) | Hovering an edge shows its completely off-screen endpoint(s); inlined `@Binds` edges expand to the interface → implementation chain. Persisted per project. |
| **Force Refresh** | Drops **all** graph caches (including PSI caches), probes for Hilt artifacts the IDE index missed and re-indexes them, re-enumerates root components, and reloads. |
| **Window** (toggle) | Moves the tool window into a floating window — useful for large graphs and multi-monitor setups. Click again to dock back. Not persisted. |

## Progress and cancellation

Graph building runs fully in the background with staged progress: **resolve (~70%) → layout (~25%) → render (~5%)**. The progress denominator is estimated from the previous build of the same component, so percentages are most accurate on repeat loads.

Selecting another component (or refreshing) cancels the in-flight build. Cancellation of very large graphs is cooperative and may take a moment to take effect.

## View state across refreshes

Refreshing or force-refreshing the **same** component preserves your zoom level and viewport anchor — the picture stays where you left it instead of snapping back to fit-to-window. Layout also seeds from the previous coordinates, so nodes you were looking at stay in place.

## Empty state

If the project contains no `@Component` (or none has been discovered yet), the window shows "No Dagger @Component found in project". Two benign causes:

- **Right after installing the plugin**: the IDE is still building Squire's binding index — the window stays empty until the one-time re-index finishes. Wait for the indexing progress bar to complete.
- **A Hilt project that was never built**: run KSP/KAPT (or a build) first.

When the tool window becomes visible again while still empty, Squire automatically performs a force refresh to pick up newly indexed or built artifacts.
