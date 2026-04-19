# Schema Tools

## `malli.util`

```clojure
(require '[malli.util :as mu])
```

Use `malli.util` to transform schema data without ad hoc vector surgery:

```clojure
(mu/get-in User [:address :zip])
(mu/update-properties [:vector :int] assoc :min 1)
(mu/optional-keys [:map [:x :int] [:y :int]])
(mu/required-keys [:map [:x {:optional true} :int]])
(mu/closed-schema schema)
(mu/open-schema schema)
(mu/merge schema-a schema-b)
```

Prefer these helpers for programmatic schema composition and refactoring. They preserve Malli semantics better than hand-editing nested vectors.

## JSON Schema

```clojure
(require '[malli.json-schema :as json-schema])

(json-schema/transform User)
```

Use JSON Schema output for API docs, client contracts, or interop with non-Clojure tooling. Review the generated schema because not every Malli feature has a perfect JSON Schema equivalent, especially custom predicates and functions.

## Swagger

```clojure
(require '[malli.swagger :as swagger])

(swagger/transform User)
```

Use Swagger/OpenAPI transformations where the surrounding stack expects Swagger2/OpenAPI-compatible schema data. Reitit projects often use Malli schemas for request/response coercion and derive OpenAPI docs from them.

## DOT and PlantUML

Malli can visualize schemas via DOT and PlantUML namespaces. Use this for large registry graphs, recursive schemas, or onboarding documentation, not as a runtime dependency.

## Schema AST

`m/ast` and `m/from-ast` convert between schema instances and map-shaped AST syntax. AST is useful for tooling, schema editors, and transformations where vector syntax is awkward.

```clojure
(-> [:string {:min 1}]
    m/schema
    m/ast
    m/from-ast)
```

Map syntax/AST is useful for tooling internals; vector syntax is usually the best authoring and persistence format.
