# Validation and Errors

## Validate

```clojure
(require '[malli.core :as m])

(m/validate :int 1)
;; => true

(m/validate :int "1")
;; => false
```

For hot paths, precompile validators:

```clojure
(def valid-user?
  (m/validator
    [:map
     [:id :uuid]
     [:name [:string {:min 1}]]]))

(valid-user? {:id #uuid "00000000-0000-0000-0000-000000000000"
              :name "Ada"})
```

Use `m/assert` at internal boundaries when throwing is appropriate. Use `m/validate` or precompiled validators for normal predicate-style checks.

## Explain

```clojure
(def Address
  [:map
   [:street :string]
   [:zip :int]
   [:lonlat [:tuple :double :double]]])

(m/explain Address {:street "Main" :lonlat [1.0 nil]})
```

Explain returns nil for valid values. Invalid results include:

| Key | Meaning |
|-----|---------|
| `:schema` | The root schema |
| `:value` | The checked value |
| `:errors` | Sequence of error maps |
| `:path` | Location inside the schema |
| `:in` | Location inside the value |
| `:type` | Malli error type, such as missing key |

Use `malli.util/explain-data` when the explain data must serialize cleanly to EDN/JSON.

## Humanize

```clojure
(require '[malli.error :as me])

(-> Address
    (m/explain {:street "Main" :lonlat [1.0 nil]})
    (me/humanize))
```

`me/humanize` turns explain data into user-facing, data-shaped error messages. Use it for UI/API errors; keep raw explain data for logs, debugging, and programmatic decisions.

## Custom Messages

Attach messages in schema properties:

```clojure
[:map
 [:size [:enum {:error/message "should be S, M, or L"} "S" "M" "L"]]
 [:age [:fn {:error/fn (fn [{:keys [value]} _]
                         (str value " should be greater than 18"))}
        #(and (int? %) (> % 18))]]]
```

Messages may be localized:

```clojure
[:string {:error/message {:en "should be a string"
                          :fi "pitaisi olla merkkijono"}}]
```

Pass custom error tables and locale to `me/humanize`:

```clojure
(me/humanize explain-data {:locale :fi})
```

## Practical Pattern

At external input boundaries:

1. Decode or coerce using a transformer.
2. Validate or use `m/coerce` to throw with explain data.
3. Humanize only at the presentation boundary.

Do not rely on transformation alone; built-in transformers are best-effort and pass unhandled bad values through.
