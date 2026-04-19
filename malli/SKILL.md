---
name: malli
description: Use when working with Malli, the data-driven schema library for Clojure, ClojureScript, and babashka. Activate when the user asks about Malli schemas, :map/:map-of/:vector/:tuple/:enum/:multi/:schema/:ref/:=> syntax, validation, m/validator, m/explain, malli.error/humanize, m/decode, m/encode, m/coerce, malli.transform transformers, generators, schema inference, registries, recursive schemas, function schemas, instrumentation, JSON Schema, Swagger, schema utilities, or Malli performance and CLJS bundle concerns.
version: 1.0.0
---

# Malli

Malli is a data-driven schema library for Clojure/Script and babashka. Schemas are ordinary data, so they can be composed, transformed, persisted, generated from, converted to JSON Schema/Swagger, and used at runtime in both CLJ and CLJS.

## Quick Decision: What do you need?

| Task | Go to |
|------|-------|
| Write schemas for maps, enums, collections, predicates, multi, recursive data | [schemas.md](schemas.md) |
| Validate values, precompile validators, explain failures, humanize errors | [validation-errors.md](validation-errors.md) |
| Decode/encode/coerce strings, JSON, defaults, extra keys, custom transformers | [transform-coerce.md](transform-coerce.md) |
| Generate sample values, use test.check, infer schemas from samples | [generation-inference.md](generation-inference.md) |
| Define function schemas, instrument/check defns, integrate clj-kondo | [function-schemas.md](function-schemas.md) |
| Reuse schemas with registries, local refs, recursive schemas, persisted EDN | [registries.md](registries.md) |
| Transform schemas with `malli.util`, JSON Schema, Swagger, DOT/PlantUML | [schema-tools.md](schema-tools.md) |
| Avoid common mistakes and performance/CLJS bundle issues | [anti-patterns.md](anti-patterns.md) |

## Core Mental Model

Malli has three layers:

- Schema data, usually vector syntax like `[:map [:id :uuid] [:name :string]]`.
- Compiled schema behavior, created by APIs such as `m/schema`, `m/validator`, `m/decoder`, `m/encoder`, and `m/coercer`.
- Registries, which resolve schema names and references such as `:my/domain-id`, `#'User`, or `[:ref ::node]`.

Use schema data at boundaries and in configuration. Precompile functions on hot paths. Treat transformation as best-effort normalization followed by validation or coercion, not as proof that input is valid.
