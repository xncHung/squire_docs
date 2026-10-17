# Getting started

> **Right after installing:** the IDE performs a one-time project re-index — Squire maintains its own index of Dagger bindings, and building it requires a full scan. **The tool window shows nothing until indexing finishes** (watch the IDE's indexing progress bar). This happens once per project; later startups are unaffected.

## Requirements

- **IntelliJ IDEA 2025.2+** or **Android Studio** (platform build 252–262), with the Kotlin plugin enabled.
- The Android plugin is **optional**: pure IntelliJ IDEA works too (build-variant detection then falls back to generated-output inspection).

## Pure Dagger projects: zero setup

Squire indexes Dagger bindings directly from your Java/Kotlin source. No build, no configuration:

1. Open the **Dagger Graph** tool window (right sidebar by default).
2. Pick a root `@Component` from the drop-down at the top.
3. The binding graph is resolved in the background and rendered when ready.

## Hilt projects: run the annotation processor first

Hilt generates part of its wiring during compilation. Squire reads these generated artifacts (`build/generated/hilt/component_sources/<variant>/`), so:

1. **Run KSP/KAPT (or `annotationProcessor`) at least once** — usually by building the project. This is required for two reasons: the component entry points come from the generated output, **and part of the bindings themselves only exist in Hilt-processor-generated sources**. Without a processor run, those bindings appear as `MISSING` nodes in the graph.
2. Open the **Dagger Graph** tool window and pick a `*_HiltComponents.*` entry point.
3. If you just switched build variants, build that variant once — see [Hilt, variants and multi-module projects](hilt-and-variants.md).

If the IDE index has not picked up freshly generated files yet, Squire detects this and re-indexes them automatically before building the graph. If the tool window was open while the first build finished, it refreshes itself when it becomes visible again.

## What you should see

- The root component as a container box, with all bindings reachable from it as nodes, laid out left-to-right in dependency layers.
- A progress overlay while resolving ("Resolving dependency graph… n%").
- Red `MISSING` nodes for requested keys that no binding can satisfy.

If nothing shows up, see [Troubleshooting](troubleshooting.md).
