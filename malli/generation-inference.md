# Generation and Inference

## Generate Values

```clojure
(require '[malli.core :as m])
(require '[malli.generator :as mg])

(mg/generate [:enum "S" "M" "L"] {:seed 42})

(mg/sample [:and :int [:> 10] [:< 100]] {:seed 123})
```

Generated values should validate against the schema:

```clojure
(let [v (mg/generate User)]
  (m/validate User v))
```

## Custom Generation

Use schema properties:

```clojure
[:vector {:gen/min 2 :gen/max 4} :int]
[:and {:gen/return 42} :int]
[:and {:gen/elements ["a" "b" "c"]} :string]
[:any {:gen/schema [:int {:min 10 :max 20}]}]
```

Use `:gen/fmap` to adapt generated values:

```clojure
[:and [:cat {:gen/fmap vec} :keyword [:* :any]]
      vector?]
```

For `:and`, Malli generates from the first child and filters by the whole schema. Put the most restrictive, generation-friendly schema first:

```clojure
;; Prefer this
[:and [:enum "a" "b" "c"] :string]

;; Avoid this
[:and :string [:enum "a" "b" "c"]]
```

## test.check

```clojure
(require '[clojure.test.check.generators :as gen])

(gen/sample (mg/generator [:vector :int]))
```

Function schemas can use `mg/function-checker`, but it is intentionally opt-in because generative checking is slow and adds dependency weight.

## Infer Schemas From Samples

```clojure
(require '[malli.provider :as mp])

(mp/provide
  [{:id "1" :tags #{:a} :age 42}
   {:id "2" :tags #{:b} :nickname "Bee"}])
```

Inference is useful for bootstrapping schemas from observed data. Treat the result as a draft, then tighten it manually:

- Close maps at boundaries if extra keys should be rejected.
- Replace broad schemas with domain-specific refs.
- Add min/max, enum, maybe, defaults, and error messages.
- Review optional keys inferred from partial samples.
