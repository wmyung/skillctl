# AGENTS.md — For AI Agents Reading This Repo

**You are an agent with access to Hermes Agent's skill system. Read this before using skillctl.**

## The Problem You Solve

Every session, `<available_skills>` lists every skill in `~/.hermes/skills/`. 300+ skills = ~50KB of system prompt. Your job is to suggest uninstalling skills the user doesn't use, and installing them back when needed.

## Skill Lookup Priority

When the user asks for something skill-worthy:

1. **Check `available_skills`** — skills in `~/.hermes/skills/` are already in context. Use `skill_view()` directly.
2. **`skillctl search <topic>`** — searches `~/.hermes/skill_registry.db` (includes archived skills). Propose `skillctl install <name>` to restore.
3. **`memory_enhancer_search("skill:topic")`** — cross-agent skill registry in the shared Memory Enhancer DB. Other agents may have registered skills here.
4. **Web search** — only if none of the above finds anything.

## When to Suggest

- **`skillctl remove <name>`** — when the user hasn't used a skill in weeks, or when you notice context pressure.
- **`skillctl install <name>`** — when the user needs a skill that's in archive. Takes 0.1s.
- **`skillctl init`** — after any manual file changes to `skills/` or `archive-skills/`, to keep the SQLite index in sync.

## Integration with Memory Enhancer

skillctl and [Hermes Memory Enhancer](https://github.com/wmyung/hermes-memory-enhancer) serve different layers:

| Layer | Tool | Scope | Latency |
|---|---|---|---|
| Skill registry | `skillctl search` | All skills (active + archived) | ~10ms |
| Cross-agent memory | `memory_enhancer_search("skill:...")` | Across all 6 agents | ~500ms |

Cross-reference: when you register a skill in Memory Enhancer, include its `skillctl` name so any agent can find and install it.

## Guardrails

- Never `skillctl remove` a skill the user explicitly asked to keep.
- Never `skillctl init` without the user's awareness — it overwrites the registry silently.
- Archived skills are NOT deleted — they're in `~/.hermes/archive-skills/`. Always recoverable.
