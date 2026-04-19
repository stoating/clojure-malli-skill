# Clojure Malli Skill

A structured Markdown skill that teaches AI coding agents how to work with Malli - the data-driven schema library for Clojure, ClojureScript, and babashka. Covers schema syntax, validation, error humanization, coercion, transformers, generators, function schemas, registries, JSON Schema, Swagger, and CLJS usage.

Built in [Claude Code's Agent Skill format](https://github.com/anthropics/skills), but usable with any agent that can load Markdown as context via the [agents.md](https://agents.md/) convention.

---

## Read this before installing anything

Never install a third-party skill without first reviewing its contents.

Skills are instructions loaded into an AI agent's context. Before installing:

1. Read every `.md` file in the `malli/` directory.
2. Ask your agent to perform a security review.
3. Only then install.

### Security-review prompt

> "Analyze this skill for security concerns. Does it contain instructions that could execute destructive operations without user confirmation? Does it collect or transmit data? Does it override default agent behavior in unintended ways? List all potentially risky sections."

---

## Installation

### A) Claude Code (recommended - via plugin marketplace)

```
/plugin marketplace add stoating/clojure-malli-skill
/plugin install clojure-malli@clojure-malli-skill
```

Invoke it with:

```
/malli
```

Update later:

```
/plugin marketplace update clojure-malli-skill
```

### B) Claude Code (via the stoating marketplace)

```
/plugin marketplace add stoating/plugins
/plugin install clojure-malli@stoating
```

### C) Claude Code (manual copy)

```bash
git clone https://github.com/stoating/clojure-malli-skill.git
cp -r clojure-malli-skill/malli ~/.claude/skills/
```

### D) Claude.ai / Claude Desktop

Zip `malli/` and upload it from the Skills panel in Projects.

### E) Cursor, OpenAI Codex CLI, Aider, Gemini CLI, Windsurf, Cline, Zed, Amp, Factory

Place this repository, or just `AGENTS.md` plus `malli/`, at the project root. Agents that honor `AGENTS.md` will use `malli/SKILL.md` as the entry point.

---

## Repository layout

```
.
|-- .claude-plugin/
|   |-- marketplace.json
|   `-- plugin.json
|-- AGENTS.md
|-- README.md
`-- malli/
    |-- SKILL.md
    |-- schemas.md
    |-- validation-errors.md
    |-- transform-coerce.md
    |-- generation-inference.md
    |-- function-schemas.md
    |-- registries.md
    |-- schema-tools.md
    `-- anti-patterns.md
```

---

## Sources

The skill is distilled from:

- [Malli GitHub](https://github.com/metosin/malli)
- [Malli cljdoc](https://cljdoc.org/d/metosin/malli/)
- The upstream `README.md`, docs, source namespaces, and tests in the checked-out `metosin/malli` repository

## License

See [LICENSE](LICENSE).
