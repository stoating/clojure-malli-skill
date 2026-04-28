# Registries, References, and Persistence

## Registry Basics

Malli resolves schema names through registries. Most public APIs accept an options map with `:registry`.

```clojure
(m/validate :string "ok" {:registry m/default-registry})
```

A registry can be a map from type/reference to schemas:

```clojure
(def registry
  {:domain/id :uuid
   :domain/name [:string {:min 1}]})

(m/validate [:map [:id :domain/id] [:name :domain/name]]
            {:id #uuid "00000000-0000-0000-0000-000000000000"
             :name "Ada"}
            {:registry registry})
```

## Local Registries

Use local registries for reusable names scoped to one schema:

```clojure
[:map {:registry {::age [:and :int [:>= 18]]}}
 [:age ::age]]
```

Local registries are usually better than mutating the global default registry in libraries and application features.

## Vars and Reusable Schemas

Var references are supported by the default var registry:

```clojure
(def UserId :uuid)

(def User
  [:map
   [:id #'UserId]
   [:friends {:optional true} [:set [:ref #'User]]]])
```

Use Vars when you want REPL-friendly reusable schemas. Use EDN/local registries when schemas must be serialized or transported.

## Recursive Schemas

Use `:schema`, local `:registry`, and `:ref`:

```clojure
[:schema {:registry {::tree [:map
                             [:value :int]
                             [:children [:vector [:ref ::tree]]]]}}
 [:ref ::tree]]
```

Put `:ref` at recursive positions. It improves behavior for parsing and generation and avoids eager expansion.

## Default Registry

`malli.registry/set-default-registry!` changes process-global behavior. Use it sparingly:

```clojure
(require '[malli.registry :as mr])

(mr/set-default-registry!
  {:string (m/-string-schema)
   :map (m/-map-schema)})
```

In applications, prefer setting the default once during startup if needed. In libraries, pass registries explicitly.

## Persistence

Use `malli.edn` to write/read schemas without `eval`:

```clojure
(require '[malli.edn :as edn])

(-> schema edn/write-string edn/read-string)
```

Serializable function forms require SCI or Cherry support. Prefer named schema refs over embedded functions when schemas need to cross process or trust boundaries.

## CLJS Bundle Size

The default registry pulls in many schema implementations. For tight CLJS bundles, initialize with a custom registry and register only needed schemas using the documented bootstrap flags:

```clojure
;; shadow-cljs
:closure-defines {malli.registry/type "custom"}
```

Only do this when bundle size matters and the app owns all schemas it needs.
