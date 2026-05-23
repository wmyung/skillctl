# skillctl — Hermes Context Saver 🧠⚡

**Zero-dependency skill manager for Hermes Agent. Trim your `available_skills`. Reclaim context. Reinstall in seconds.**

Every skill in `~/.hermes/skills/` injects its name and description into the system prompt every session. 300+ skills means ~50KB of context burned before the model reads a single user message. **skillctl moves unused skills to an archive directory so they stop consuming context, and brings them back on demand.**

## The Problem

Hermes Agent uses progressive disclosure for skill *content* (`skill_view()` loads full SKILL.md on demand), but **skill names and descriptions are always listed** in `<available_skills>`. With 300+ installed skills:

| Component | Size |
|---|---|
| `available_skills` ~300 entries | ~50 KB |
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
| **skillctl (this)** | **0** | Hermes-only | ✅ Instant | ✅ SQLite | ✅ Native |
| [Hermes Curator](https://hermes-agent.nousresearch.com/docs/user-guide/features/curator) | Hermes | Hermes-only | ⏳ 30/90d | ❌ | ✅ Built-in |
| [agent-skill-manager](https://github.com/luongnv89/agent-skill-manager) | Node.js | 17 providers | ✅ | ✅ | ❌ |
| [Skills-ContextManager](https://github.com/One-Man-Company/Skills-ContextManager) | Python+Node | MCP apps | ✅ Toggle | ✅ | ❌ |
| [Curator Evolver](https://github.com/pingchesu/hermes-curator-evolver) | Hermes plugin | Hermes-only | ⏳ Evidence | ✅ | ✅ Plugin |

skillctl's niche: **the leanest Hermes-native skill toggler — zero dependencies, one file, instant on/off.**

## Recommended Workflow

Most users don't know which skills they use — they discover it through conversation. Here's the pattern that works:

```text
User: "There are too many skills"  (feeling context pressure)
Agent: skillctl status  → "250 active, 120 archived. Want me to suggest what to archive?"

User: "Sure, what do you have in mind?"
Agent: Groups skills by category, estimates usage, proposes archive candidates

User: "Keep those, archive the rest"
Agent: skillctl remove <list>  → "150 left. Reinstall anytime with `skillctl install <name>`"
```

**Key insight:** Users rarely pre-sort skills. They respond to concrete proposals. Let the agent:
1. Scan & group active skills
2. Propose archive candidates with rationale
3. Let the user confirm/reject per category
4. Execute with `skillctl remove`
5. Show the before/after diff

This conversational cleanup takes 2-3 minutes and can be repeated monthly.

## For Agents (Read This)

**How to use skillctl when helping a user:**

1. **User asks for something a skill might handle** — check `available_skills` first (already in context). If found, use `skill_view()`.
2. **Not in available_skills?** — suggest `skillctl search <topic>`. This searches both active and archived skills via SQLite.
3. **Found in archive?** — suggest `skillctl install <name>`. Takes ~0.1s.
4. **Not in skillctl's index either?** — fall back to web search or other resources.

**When to suggest archiving:**
- The user hasn't used a skill in weeks.
- You notice context pressure from too many active skills.
- A skill was only needed once for a one-off task.

**Important:** Archived skills are NOT deleted. They live in `~/.hermes/archive-skills/` and are fully recoverable with `skillctl install`.

## Optional: Memory Enhancer Integration

If you also use [Hermes Memory Enhancer](https://github.com/wmyung/hermes-memory-enhancer), you can cross-reference skillctl with the shared memory DB:

```
memory_enhancer_search("skill:postgwas")  → finds descriptions
skillctl install postgwas-comprehensive-report-builder  → activates it
```

Without the enhancer, skillctl works just fine — all search is local via SQLite.

## Install

```bash
curl -sL https://raw.githubusercontent.com/wmyung/skillctl/main/skillctl -o skillctl
chmod +x skillctl
./skillctl init
```

**Requires:** Python 3.8+, `~/.hermes/skills/` directory (Hermes Agent).

## Keywords

`hermes-agent` `context-optimization` `skill-manager` `prompt-compression` `available-skills` `sqlite` `agent-tooling` `llm-context` `zero-dependency` `token-saver`
