# Troubleshooting

## Refresh vs. Force Refresh vs. Invalidate Caches

Three escalation levels, cheapest first:

1. **Refresh** (toolbar) — rebuilds only the current component's graph. Use after editing code: binding-relevant PSI changes normally invalidate caches automatically, so a plain refresh picks them up.
2. **Force Refresh** (toolbar) — drops all graph caches, probes and re-indexes missed Hilt artifacts, and re-enumerates root components. Use after switching build variants, after a Gradle sync, or when components you expect are missing from the picker.
3. **Tools → Invalidate Caches and Rebuild Dagger Index** — the nuclear option: requests a full rebuild of Squire's Dagger index (IDE-wide re-indexing, i.e. a dumb-mode period), drops all graph caches, and refreshes automatically once indexing finishes. Use when the graph is wrong in ways neither refresh nor force refresh can fix — e.g. index drift or corruption after a hot plugin update.

## Common symptoms

**"No Dagger @Component found in project"**
- **Right after installing the plugin**: the IDE is performing a one-time re-index to build Squire's binding index, and the tool window shows nothing until it finishes. Wait for the indexing progress bar — this is expected, and happens once per project.
- Hilt project that was never built: run KSP/KAPT (or a build), then reopen the tool window (it auto-refreshes when it becomes visible while empty).
- Pure Dagger project: make sure your components are in **production** sources — test sources are excluded from resolution.

**Missing bindings (`MISSING` nodes) you believe exist**
- Hilt project: make sure KSP/KAPT (or `annotationProcessor`) has actually run for the **current** variant — some bindings only exist in Hilt-processor-generated sources, so an unbuilt variant produces `MISSING` nodes even though the component is listed.
- After a variant switch or sync, the IDE index can lag: try **Force Refresh**.
- If the problem started right after a **plugin update**: restart the IDE first (a hot-updated plugin can leave the index half-invalidated); if it persists, run **Tools → Invalidate Caches and Rebuild Dagger Index**.
- Check the [known limitations](limitations.md): the binding may use a construct Squire does not model (e.g. Dagger Producers).

**Graph does not reflect code changes**
- Use **Refresh**. Squire tracks PSI dependencies and invalidates automatically, but generated-code changes (new KSP/KAPT output) sometimes need a force refresh.

**A gutter icon click shows "Node not in the graph of the selected root component"**
- The node belongs to a different root component (or a different variant's component). Switch the component picker.

**The component list is empty or stale after building**
- Open/close the tool window or press **Force Refresh** — the picker re-enumerates components from the generated output.

## Diagnostic info to include in a bug report

- IDE and version, Squire version.
- Hilt or pure Dagger; KSP or KAPT; the selected build variant.
- The IDE log excerpt around the failure (`Help → Show Log`), in particular lines from `DaggerComponentService` / `DaggerGraphPanel`.
- A screenshot of the graph or the missing part of it.
