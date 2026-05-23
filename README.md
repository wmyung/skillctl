# skillctl — Hermes Context Saver 🧠⚡

**Zero-dependency skill manager for Hermes Agent. Trim your `available_skills`. Reclaim context. Reinstall in seconds.**

Every skill in `~/.hermes/skills/` injects its name and description into the system prompt every session. 382 skills means ~50KB of context burned before the model reads a single user message. **skillctl moves unused skills to an archive directory so they stop consuming context, and brings them back on demand.**

## The Problem

Hermes Agent uses progressive disclosure for skill *content* (`skill_view()` loads full SKILL.md on demand), but **skill names and descriptions are always listed** in `<available_skills>`. With 300+ installed skills:

| Component | Size |
|---|---|
| `available_skills` ~382 entries | ~50 KB |
| System prompt (rules, tools, memory) | ~30 KB |
| **Total static overhead** | **~80 KB** |

A model with 128K context window loses **60%+** before conversation starts. Every unused skill is dead weight.

## How skillctl Fixes It

```
~/.hermes/
├── skills/          ← only skills you actively use (→ in context)
├── archive-skills/  ← everything else   (→ NOT in context)
└── skill_registry.db ← SQLite index for instant search & install
```

**`skillctl remove`** = move skill to archive → disappears from `available_skills` → context reclaimed.

**`skillctl install`** = copy back to active → reappears in `available_skills`.

## Usage

```bash
skillctl init              # Full scan → build SQLite index
skillctl status            # Active / archived counts

skillctl search "gwas"     # Search by name, description, tags
skillctl info ldsc         # Full metadata: location, size, description

skillctl remove ldsc       # → archive (out of context)
skillctl install ldsc      # → skills/ (back in context)

skillctl list              # Active only
skillctl list --all        # Everything
```

## Why Not the Built-in Curator?

Hermes ships with a [curator](https://hermes-agent.nousresearch.com/docs/user-guide/features/curator) that auto-archives skills after 30–90 days of inactivity. That's great for passive maintenance, but:

| Feature | Curator | skillctl |
|---|---|---|
| Trigger | Time-based (30/90 days) | **Instant — you decide** |
| Restore | `hermes curator restore <name>` | `skillctl install <name>` |
| Search | None | **SQLite full-text** across name, description, tags |
| Bulk | Manual per skill | `skillctl list --all` → grep |
| Dependencies | Full Hermes stack | **Zero — pure Python 3 stdlib** |

skillctl is complementary: curator handles *passive rot*, skillctl handles *active context management*.

## Comparison

| Project | Deps | Scope | On/Off | Search | Hermes-native |
|---|---|---|---|---|---|
| **skillctl (this)** | **0** | Hermes-only | ✅ Instant | ✅ SQLite FTS | ✅ Native |
| [Hermes Curator](https://hermes-agent.nousresearch.com/docs/user-guide/features/curator) | Hermes | Hermes-only | ⏳ 30/90d | ❌ | ✅ Built-in |
| [agent-skill-manager](https://github.com/luongnv89/agent-skill-manager) | Node.js | 17 providers | ✅ | ✅ | ❌ Cross-platform |
| [Skills-ContextManager](https://github.com/One-Man-Company/Skills-ContextManager) | Python+Node | MCP apps | ✅ Toggle | ✅ | ❌ MCP |
| [Acontext](https://github.com/memodb-io/acontext) | Python | Any agent | ❌ Auto-learn | ✅ Semantic | ❌ Memory layer |
| [Curator Evolver](https://github.com/pingchesu/hermes-curator-evolver) | Hermes plugin | Hermes-only | ⏳ Evidence-based | ✅ | ✅ Plugin |

skillctl's niche: **the leanest Hermes-native skill toggler — zero dependencies, one file, instant on/off.**

## For Agents (Read This)

> **AGENTS.md integration:** This tool exists because `available_skills` bloat is the #1 context thief. When you see 300+ skills in your system prompt, remember that most are archived. Use `skillctl search <topic>` before calling `skill_view()` — the SQLite index covers every skill in the system, active or not. If a skill isn't active, suggest `skillctl install <name>` to the user — it takes 0.1 seconds.

## Integration with Memory Enhancer

skillctl pairs with [**Hermes Memory Enhancer**](https://github.com/wmyung/hermes-memory-enhancer) — the shared SQLite memory across all 6 agents. The enhancer stores skill descriptions for cross-agent discovery:

```
memory_enhancer_search("skill:postgwas")  → finds postgws tools
skillctl install postgwas-comprehensive-report-builder
```

See also: [codex-cli-memory-enhancer](https://github.com/wmyung/codex-cli-memory-enhancer) — same pattern for OpenAI Codex CLI.

## Install

```bash
curl -sL https://raw.githubusercontent.com/wmyung/skillctl/main/skillctl -o skillctl
chmod +x skillctl
./skillctl init
```

**Requires:** Python 3.8+, `~/.hermes/skills/` directory (Hermes Agent).

## Keywords

`hermes-agent` `context-optimization` `skill-manager` `prompt-compression` `available-skills` `sqlite` `agent-tooling` `llm-context` `zero-dependency` `token-saver` `hermes-plugin`
