# skillctl — Hermes Context Saver 🧠⚡

**Trim your Hermes Agent's `available_skills`. Reclaim context. Reinstall in seconds.**

Every skill in `~/.hermes/skills/` gets its name + description injected into the system prompt every session. 300+ skills = ~50KB of dead weight. skillctl moves unused skills to an archive directory so they stop consuming context, and brings them back when you need them.

- **Zero dependencies** — pure Python 3 stdlib, single file, no pip install
- **SQLite registry** — full-text search across name, description, and tags
- **Instant on/off** — `install` copies archive → skills/, `remove` does the reverse
- **Pairs with [Hermes Memory Enhancer](https://github.com/wmyung/hermes-memory-enhancer)** — save skill descriptions there too for cross-agent discovery

## Usage

```bash
skillctl init              # scan skills/ + archive/ → build SQLite index
skillctl status            # active 257 / archived 125

skillctl search "gwas"     # search by name, description, or tags
skillctl remove ldsc       # → archive (out of context)
skillctl install ldsc      # → skills/ (back in context)

skillctl list              # active only
skillctl list --all        # everything
```

## Why

| Before | After |
|---|---|
| 382 skills in `available_skills` | **257** (only active) |
| ~50KB in system prompt | **~25KB** |
| All descriptions always visible | Search + install on demand |

## Install

```bash
curl -sL https://raw.githubusercontent.com/wmyung/skillctl/main/skillctl -o skillctl
chmod +x skillctl
./skillctl init
```

**Requires:** Python 3.8+, a `~/.hermes/skills/` directory (Hermes Agent).

## Related

- [hermes-memory-enhancer](https://github.com/wmyung/hermes-memory-enhancer) — persistent SQLite memory across sessions
- [codex-cli-memory-enhancer](https://github.com/wmyung/codex-cli-memory-enhancer) — same for OpenAI Codex CLI

## Keywords

`hermes-agent` `context-optimization` `skill-manager` `prompt-compression` `sqlite` `agent-tooling` `llm-context` `token-saver` `zero-dependency`
