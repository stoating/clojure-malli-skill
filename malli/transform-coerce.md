# Transformation and Coercion

## Decode and Encode

```clojure
(require '[malli.core :as m])
(require '[malli.transform :as mt])

(m/decode :int "42" mt/string-transformer)
;; => 42

(m/encode :int 42 mt/string-transformer)
;; => "42"
```

Precompile for repeated use:

```clojure
(def decode-user
  (m/decoder
    [:map [:id :uuid] [:active :boolean]]
    mt/string-transformer))
```

## Built-In Transformers

| Transformer | Use |
|-------------|-----|
| `mt/string-transformer` | Strings to/from EDN-like values |
| `mt/json-transformer` | JSON-compatible values to/from EDN values |
| `mt/strip-extra-keys-transformer` | Drop keys not in closed/open map schema entries |
| `mt/default-value-transformer` | Apply schema defaults |
| `mt/key-transformer` | Encode/decode map keys |
| `mt/collection-transformer` | Convert between collection shapes |

Transformers compose:

```clojure
(mt/transformer
  mt/strip-extra-keys-transformer
  mt/string-transformer
  mt/default-value-transformer)
```

Order matters. For tagged unions, decode dispatch keys before branch validation:

```clojure
[:multi {:dispatch :type
         :decode/string #(update % :type keyword)}
 [:user [:map [:type [:= :user]] [:name :string]]]]
```

## Coercion

`m/coerce` decodes and validates, throwing on invalid input:

```clojure
(m/coerce :int "42" mt/string-transformer)
;; => 42

(m/coerce :int "invalid" mt/string-transformer)
;; throws :malli.core/invalid-input
```

Precompile:

```clojure
(def coerce-user
  (m/coercer User (mt/transformer
                    mt/strip-extra-keys-transformer
                    mt/json-transformer)))
```

Exception-free continuation style is available:

```clojure
(m/coerce schema value transformer on-success on-error)
```

## Custom Transformers

Define a transformer by schema type:

```clojure
(def ints-as-strings
  (mt/transformer
    {:name :ints-as-strings
     :encoders {:int #(apply str (repeat % \#))}
     :decoders {:int #(if (string? %) (count %) %)}}))
```

Or attach named transformer behavior to schema properties:

```clojure
[:map
 [:name [:string {:decode/custom clojure.string/trim}]]]
```

Then use a matching named transformer:

```clojure
(m/decode schema value (mt/transformer {:name :custom}))
```

## Boundary Pattern

For HTTP JSON:

```clojure
(def Request
  [:map {:closed true}
   [:id :uuid]
   [:limit {:optional true :default 50} :int]])

(def parse-request
  (m/coercer
    Request
    (mt/transformer
      mt/strip-extra-keys-transformer
      mt/json-transformer
      mt/default-value-transformer)))
```

Use closed maps when stripping unknown keys is part of the contract. Use open maps when extra keys are intentionally preserved.
