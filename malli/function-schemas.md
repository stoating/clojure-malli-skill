# Function Schemas

## Function Values

Malli describes function values with `:=>` and `:function`.

```clojure
[:=> [:cat :int :int] :int]

[:function
 [:=> [:cat :int] :int]
 [:=> [:cat :int :int [:* :int]] :int]]
```

The input side is a sequence schema, usually `:cat` or `:catn`.

```clojure
[:=> [:catn [:x :int] [:y :int]] :int]
```

Plain validation of a `:=>` schema checks that the value is a function. It does not prove argument or return behavior unless a function checker is configured.

## Generative Function Checking

```clojure
(require '[malli.core :as m])
(require '[malli.generator :as mg])

(def Plus
  (m/schema
    [:=> [:cat :int :int] :int]
    {::m/function-checker mg/function-checker}))

(m/validate Plus +)
```

Use this in tests or development checks, not on hot request paths.

## Guards

Guards validate the argument/return pair:

```clojure
[:=> [:cat :int]
 :int
 [:fn {:error/message "return should be greater than arg"}
  (fn [[[arg] ret]] (> ret arg))]]
```

## Defn Schemas

Malli supports defn schemas and instrumentation through its function-schema tooling. Use them when you want function contracts close to implementation and optional development/runtime checking.

Practical guidance:

- Keep public boundary functions annotated first.
- Do not instrument every internal helper in production by default.
- Use clj-kondo integration for static feedback where available.
- Use runtime instrumentation during development, tests, or specific debugging sessions.

## ClojureScript

ClojureScript instrumentation has separate runtime and build-tool concerns. Check whether the project uses shadow-cljs, cljs build hooks, or Malli CLJS instrumentation docs before changing config. Keep instrumentation preloads/dev-only where possible to avoid production bundle cost.
