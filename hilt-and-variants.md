# Hilt, variants and multi-module projects

## How Squire sees Hilt

Hilt's components are generated at compile time. Squire indexes the generated `*_HiltComponents.java` artifacts under `build/generated/hilt/component_sources/<variant>/` and treats them as root components. Bindings declared in your modules (`@Module`, `@AndroidEntryPoint` members injection, etc.) are resolved from source and from library bytecode (jar/aar classes, e.g. Hilt's own system modules, are indexed via ASM).

Consequences:

- **Run KSP/KAPT (or `annotationProcessor`) at least once** before expecting a graph. The generated output is the source of truth for the component list — and part of the bindings themselves are produced by the Hilt processor into generated sources, so without a processor run the graph loses those bindings (they show up as `MISSING` nodes).
- Test variants (`*UnitTest`, `*AndroidTest`) are hidden by default. Enable **Include test modules** in the toolbar to list them; their graphs resolve against the owning module's test classpath. Hilt custom test components are attributed to their owning test class in the entry tooltip; shared test roots are marked as shared.
- No build output for any variant (e.g. a fresh checkout): no Hilt components can be listed yet.

## Build variants

- The component picker marks components of the **current build variant** with `*` and pre-selects them. Test components of that variant are starred as well, but a production component is always pre-selected over a test one.
- Variant detection is **per module**: an app module and the libraries it includes each follow their own selected variant from the IDE's Build Variants panel.
- When the Android plugin is absent (plain IntelliJ IDEA), variant detection falls back to the most recently modified `component_sources/<variant>` directory.
- Components that were never built for the current variant can still be listed (via build-directory location heuristics); their graphs may be incomplete until you build that variant.

## Multi-module projects

Same-named components across modules and variants are distinct entries, disambiguated in the picker by variant, package and module suffixes. Graph resolution is scoped to the component's own module and variant, so identically named modules from another variant do not leak into the graph.

## Index self-healing

The IDE index occasionally misses generated Hilt artifacts — most often right after a variant switch, when the same generated file paths flip between variants. Squire probes the expected artifacts before building a graph and forces re-indexing of the missing ones (this takes milliseconds; a log line reports `repaired N unindexed hilt artifacts`).

If a graph still looks wrong after a variant switch, see [Troubleshooting](troubleshooting.md).
