# Malli Anti-Patterns

## Using Transformation As Validation

Built-in transformers are best-effort and pass bad values through when they do not know how to transform them. Always validate after decode, or use `m/coerce`/`m/coercer`.

## Leaving Boundary Maps Open Accidentally

Malli maps are open by default. For external API requests, DB commands, and security-sensitive data, consider `[:map {:closed true} ...]` or a transformer that strips unknown keys before validation.

## Hand-Editing Complex Schema Vectors

For programmatic changes, use `malli.util` helpers like `mu/get-in`, `mu/update-in`, `mu/merge`, `mu/closed-schema`, and `mu/optional-keys`. Hand-rolled vector edits are brittle around properties, refs, and map entries.

## Global Registry Mutation In Libraries

Do not call `mr/set-default-registry!` from library code. Accept a registry option, use local registries, or expose schemas that applications can compose.

## Recursive Schemas Without `:ref`

Recursive schemas need refs in recursive positions:

```clojure
[:schema {:registry {::node [:map [:children [:vector [:ref ::node]]]]}}
 [:ref ::node]]
```

Omitting `:ref` can cause eager expansion and stack overflows.

## Predicate Schemas When Properties Matter

`string?` validates strings, but it cannot use Malli properties such as `:min` or `:max`. Use `[:string {:min 1}]` when properties matter.

## Slow Function Checking In Production Paths

`mg/function-checker` uses generative testing. Keep it in tests, development instrumentation, or focused debugging. Do not run it per request unless the cost is intentional.

## Poor `:and` Generator Ordering

Malli generates from the first `:and` child and filters by the full schema. Put the generator-friendly constrained schema first:

```clojure
[:and [:enum "a" "b"] :string]
```

## Persisting Unsafe Function Schemas

Schemas are data, but embedded functions are code. Prefer named refs and declarative schemas for persisted/untrusted schemas. Use SCI/Cherry-based function deserialization only when the trust and dependency model is explicit.

## Ignoring CLJS Registry Size

The default registry can increase CLJS bundle size. For small browser bundles, consider a custom registry bootstrap and register only the schemas the app uses.
