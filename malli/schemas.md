# Malli Schemas

## Basic Shape

```clojure
(require '[malli.core :as m])

(def User
  [:map
   [:id :uuid]
   [:name [:string {:min 1}]]
   [:email {:optional true} :string]
   [:roles [:set :keyword]]])

(m/validate User {:id #uuid "00000000-0000-0000-0000-000000000000"
                  :name "Ada"
                  :roles #{:admin}})
```

Prefer vector syntax for persisted and shared schemas. Use predicate schemas such as `string?` only when schema properties like `:min` and `:max` are not needed; prefer `:string`, `:int`, etc. when properties matter.

## Common Types

| Schema | Use |
|--------|-----|
| `:any`, `:nil`, `:boolean`, `:int`, `:double`, `:string`, `:keyword`, `:uuid` | Built-in scalar schemas |
| `[:= value]` | Exact value |
| `[:enum "S" "M" "L"]` | One of fixed values |
| `[:maybe schema]` | Value can be nil or match schema |
| `[:and schema ...]` | All schemas must match |
| `[:or schema ...]` | Any schema may match |
| `[:not schema]` | Value must not match |
| `[:fn pred]` | Custom predicate |

When an `:enum` value itself is a map or nil, include nil properties to disambiguate:

```clojure
[:enum nil {}]
[:enum nil nil]
```

## Maps

Maps are open by default:

```clojure
(m/validate [:map [:x :int]] {:x 1 :extra true})
;; => true
```

Close them explicitly at API, persistence, or security boundaries:

```clojure
[:map {:closed true}
 [:x :int]]
```

Optional keys use properties on the map entry:

```clojure
[:map
 [:id :uuid]
 [:nickname {:optional true} :string]]
```

Use `:map-of` for homogeneous map indexes:

```clojure
[:map-of :string [:map [:lat :double] [:lon :double]]]
```

Use `::m/default` for extra keys with a default schema:

```clojure
[:map
 [:known :int]
 [::m/default [:map-of :keyword :string]]]
```

## Collections and Sequences

```clojure
[:vector :int]                  ; vector of ints
[:set :keyword]                 ; set of keywords
[:sequential :string]           ; list/vector/seq, homogeneous
[:tuple :double :double]        ; fixed vector shape
[:cat :string :int]             ; sequence expression
[:catn [:name :string] [:age :int]]
[:* :int]                       ; zero or more
[:+ :int]                       ; one or more
[:? :int]                       ; optional
[:repeat {:min 2 :max 4} :int]
```

Prefer `:vector`, `:set`, and `:sequential` when the collection is homogeneous and simple. Use sequence regex schemas only when position, repetition, or alternatives are part of the data contract.

## Multi and Recursive Schemas

Use `:multi` for tagged unions:

```clojure
[:multi {:dispatch :type}
 [:user [:map [:type [:= :user]] [:name :string]]]
 [:team [:map [:type [:= :team]] [:members [:vector :uuid]]]]]
```

Use `::m/default` for fallback branches when the union is intentionally open.

Recursive schemas require a local registry and `:ref` in recursive positions:

```clojure
[:schema {:registry {::node [:map
                             [:value :int]
                             [:children [:vector [:ref ::node]]]]}}
 [:ref ::node]]
```

Without `:ref`, recursive schemas can eagerly expand until stack overflow.
