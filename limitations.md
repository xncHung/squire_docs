# Known limitations

These follow from Dagger semantics or from the current scope of Squire's static model. They are intentional renderings, not bugs — but if one of them blocks you, please file an issue.

## Dagger semantics

- **Dagger Producers are not supported** (`@Produces`, `@ProductionComponent`, `@ProducerModule`).
- **`javax.inject` only.** `jakarta.inject` annotations are not indexed (navigation partially resolves them, but they do not appear in graphs).
- **`@AssistedInject` / `@AssistedFactory`**: qualifiers are not supported and scopes have no effect — matching Dagger's own semantics, where the assisted-injection request targets the factory type only.
- **`? super T` / Kotlin `in T` wildcards** are not supported (Dagger itself rejects them as binding keys); they degrade to the erased raw type.
- **Custom `@MapKey` values** (anything beyond `@StringKey` / `@IntKey` / `@LongKey` / `@ClassKey` / `@LazyClassKey`) are not annotated on contribution edges.
- **Multibinding declarations with generic declaration sites** are not modeled.
- **Bindings from generic methods or generic declaring classes** degrade to erased raw types in bytecode-indexed libraries.

## Resolution scope

- **Test sources are excluded by default.** Enable **Include test modules** in the toolbar to list and graph unit-test / instrumented-test components. Production graphs never see test bindings.
- **`@BindValue` fields are not indexed as declarations** — no gutter icon is shown on the field. The bindings Hilt generates for them appear in the graph as ordinary provides; navigation lands on the generated `<Test>_BindValueModule.provides<Field>` method (from whose body you can step on to the test field). `@TestInstallIn` modules are modeled normally.
- **Blended test classpath when unit and instrumented tests share a module.** A test component is resolved against its owning module's test classpath. Where the IDE models each Gradle source set as its own module (the Gradle/AGP default), unit-test and instrumented-test sources stay separate; in projects that keep both in a single module the two test classpaths are merged, so a binding that exists in only one of them may appear in the other's graph.
- **A Hilt variant that was never built** cannot contribute its generated artifacts; the component may still be listed (via build-directory heuristics) but its graph can be incomplete — in particular, bindings that only exist in Hilt-processor-generated sources will be missing until KSP/KAPT has run for that variant.
- Graphs are resolved within the owning module's **runtime classpath** (the module itself, its transitive dependencies, libraries, and the SDK) — matching where Dagger's generated code actually runs and what Hilt's build-time aggregation collects. `compileOnly` (provided-by-environment) dependencies are included: they are legal Dagger bindings supplied at runtime by the environment (e.g. customized ROM/JRE). Bindings in modules outside that classpath do not appear, which is what keeps same-named classes in sibling modules or sibling app modules from leaking into each other's graphs.
- **Non-selected build variants are not indexed.** You can view and graph a component of any variant, but the IDE only indexes the currently selected one. Hilt's generated components are read from the selected root's variant, while module methods and `@Inject` constructors resolve against the IDE's current variant — a non-selected variant's classes cannot be found by class lookup (only by file). When two variants provide the same class differently, graph → source navigation may land on the current variant's declaration. The graph shows a hint when the selected component belongs to a variant other than the current one. Variant-aware resolution is intentionally not attempted: AGP's source-set matching (a `demoDebug` declaration may come from `demo`, `debug`, or `demoDebug`) combined with the IDE's navigation constraints makes it disproportionately complex for the benefit.

## Presentation

- Node labels use short type names; same-named types from different packages are not disambiguated in labels (the navigation target is still exact).
- Long label lines are rendered on a single line (node width adapts to content); pathologically long lines (over 100 chars) are truncated with an ellipsis. Hover/navigation remains available.
- Gutter icons (source → graph) do not appear in Hilt generated files under `build/generated/hilt/component_sources/` — the IDE does not treat these excluded directories as sources, so no line markers are computed there. Graph → source navigation into these files still works.
- **`@BindsOptionalOf` may appear as several nodes** in one graph — some `MISSING`, some actually bound (one per request site state). Gutter-to-graph navigation lands on an arbitrary one of them. A proper fix (e.g. a chooser popup when several nodes match) is disproportionately complex for the gain, so this is accepted as-is.
