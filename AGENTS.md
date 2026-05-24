# AGENTS.md — For AI Agents Reading This Repo

**You are an agent with access to Hermes Agent's skill system. Read this before using skillctl.**

## The Problem You Solve

Every session, `<available_skills>` lists every skill in `~/.hermes/skills/`. 300+ skills = ~50KB of system prompt. Your job is to suggest uninstalling skills the user doesn't use, and installing them back when needed.

## Skill Lookup Priority

When the user asks for something skill-worthy:

1. **Check `available_skills`** — skills in `~/.hermes/skills/` are already in context. Use `skill_view()` directly.
2. **`skillctl search <topic>`** — searches `~/.hermes/skill_registry.db` (includes archived skills). Propose `skillctl install <name>` to restore. Works without any extras.
3. **Web search** — only if skillctl finds nothing.

## When to Suggest

- **`skillctl remove <name>`** — when the user hasn't used a skill in weeks, or when you notice context pressure.
- **`skillctl install <name>`** — when the user needs a skill that's in archive. Takes 0.1s.
- **`skillctl init`** — after any manual file changes to `skills/` or `archive-skills/`, to keep the SQLite index in sync.

## Optional: Cross-Reference with Sibling Tools

skillctl is part of a zero-dependency Hermes tool suite:

| Project | Purpose |
|---|---|
| [codex-cli-memory-enhancer](https://github.com/wmyung/codex-cli-memory-enhancer) | Persistent memory for Codex CLI — per-project SQLite |
| [Hermes Memory Enhancer](https://github.com/wmyung/hermes-memory-enhancer) | Cross-session semantic memory via `memory_enhancer_*` tools |
| **skillctl (this)** | Skill registry search via `skillctl search` |
| [Hermes SQLite Toolkit](https://github.com/wmyung/hermes-sqlite-toolkit) | Tool cache + artifact registry + decision log via `sqlite_query` |

### Memory Enhancer Integration

If you also have Hermes Memory Enhancer installed, cross-reference:

| Layer | Tool | Scope |
|---|---|---|
| Skill registry | `skillctl search` | All skills (active + archived), ~10ms |
| Cross-agent memory | `memory_enhancer_search("skill:...")` | If multiple agents share a DB, ~500ms |
| Structured data | `sqlite_query(database="skill", query="...")` | Direct SQL on skill_registry.db |

Without the enhancer or toolkit, skillctl works standalone. All search is local via SQLite.

## Guardrails

- Never `skillctl remove` a skill the user explicitly asked to keep.
- Never `skillctl init` without the user's awareness — it overwrites the registry silently.
- Archived skills are NOT deleted — they're in `~/.hermes/archive-skills/`. Always recoverable.
