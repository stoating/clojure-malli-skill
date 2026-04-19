# Agent instructions

This repository contains the **`malli`** skill - a structured set of Markdown files that teach an AI coding agent how to work with Malli, the data-driven schema library for Clojure, ClojureScript, and babashka.

**Any agent that understands this `AGENTS.md` convention should:**

1. Treat `malli/SKILL.md` as the entry point - it contains a decision table pointing to the right reference file for the task.
2. Load reference files on demand based on that table:
   - `schemas.md` - schema syntax for maps, collections, multi, recursive data
   - `validation-errors.md` - validation, explain data, humanized errors
   - `transform-coerce.md` - decode, encode, coercion, transformers
   - `generation-inference.md` - generators, test.check, schema inference
   - `function-schemas.md` - function schemas, instrumentation, clj-kondo
   - `registries.md` - registries, refs, persistence, CLJS registry size
   - `schema-tools.md` - `malli.util`, JSON Schema, Swagger, AST, diagrams
   - `anti-patterns.md` - common mistakes and performance traps
3. Clarify whether the task is about internal domain modeling, external input validation, coercion, docs generation, testing, or instrumentation before changing schemas.
4. Prefer schema data and Malli utility APIs over ad hoc nested vector manipulation.
5. Precompile validators/decoders/coercers on hot paths.

This file follows the [agents.md](https://agents.md/) convention and is honored by OpenAI Codex CLI, Cursor, Aider, Zed, Amp, Gemini CLI, Google Jules, Windsurf, Factory, RooCode, and many others.

For Claude Code, the richer native format is `.claude-plugin/` + `malli/SKILL.md`.
