# 🌀 Shinobi Agents

Claude Code subagents themed on the legendary shinobi of Naruto. You are the **Hokage** — 8 legendary agents cover the full dev lifecycle (recon → research → plan → build → debug → review → QA → performance), each commanding a squad of 2 shinobi subagents. Every report ends with a **Next shinobi:** recommendation; you always do the dispatching.

```
1. kakashi   → scouts the codebase (recon)
2. tobirama  → refines the requirement into a mission plan
3. hashirama → builds it, tests green
4. itachi    → reviews + security
5. back to you → ship
```

Support: **jiraiya** (external research) · **tsunade** (debugging) · **orochimaru** (adversarial QA) · **minato** (performance).

See [docs/shinobi-roster.md](docs/shinobi-roster.md) for the full mermaid lifecycle diagram, per-agent reference, and squads.

## The roster

| Legendary | Role | Squad (shinobi, max 2) |
|---|---|---|
| `kakashi` | codebase recon & intel briefings | neji (structure sweeps) · hinata (precise lookups) |
| `jiraiya` | deep external research | konan (gathering) · nagato (synthesis) |
| `tobirama` | requirements refinement & planning | ino (intel relay) · shikamaru (strategy) |
| `hashirama` | feature implementation | yamato (structure) · naruto (volume) |
| `tsunade` | debugging & root-cause fixes | shizune (reproduction) · sakura (diagnosis) |
| `itachi` | code review & security | shisui (spec fidelity) · sasuke (security) |
| `orochimaru` | adversarial QA & edge cases | kabuto (experiment design) · deidara (stress runs) |
| `minato` | performance & resource optimization | rocklee (benchmarks) · obito (profile analysis) |

## Install

1. Copy the agents into your project (or `~/.claude/agents/` for user-wide). The `legendary/` + `shinobi/` split is repo organization only — Claude Code reads them flat from `.claude/agents/`:

```sh
cp agents/legendary/*.md agents/shinobi/*.md /path/to/your/project/.claude/agents/
```

2. Merge `settings.json` into your project's `.claude/settings.json` — it caps subagent spawn depth at 2, so legendaries can summon their squad but shinobi can never spawn further:

```json
{
  "env": {
    "CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH": "2"
  }
}
```

3. Restart Claude Code, then dispatch from the agents menu. Try: *"dispatch kakashi — map how auth flows through my API."*

## Skill wiring

The legendaries reference skills from three plugins (all free, official marketplaces). The agents work without them — skill lines are simply ignored if a plugin is missing — but they're stronger with them:

```sh
claude plugins install mattpocock-skills
```

plus the `superpowers` and `ponytail` plugins from the `claude-community` marketplace.

| Agent | Skills referenced |
|---|---|
| tobirama | `brainstorming`, `writing-plans`, `grill-with-docs`, `to-spec`, `to-tickets` |
| hashirama | `tdd`, `implement`, `executing-plans`, `codebase-design`, ponytail discipline |
| tsunade | `systematic-debugging`, `diagnosing-bugs` |
| itachi | `code-review`, `verification-before-completion`, ponytail-review lens |
| orochimaru | `tdd`, `prototype` |
| minato | `diagnosing-bugs`, ponytail-review lens |
| jiraiya | `research`, `grilling` |
| kakashi | — (hands external research to jiraiya) |

## How squads work

- **Legendaries** (`model: inherit`) may spawn **max 2** named shinobi (`model: sonnet`) — the pair is listed in each legendary's `## Squad` section.
- **Shinobi** have no `Agent` tool and can never spawn subagents (platform-enforced via tools + depth cap).
- Every shinobi is also directly dispatchable when a single focused strike is enough.
- Squad dispatch order is encoded in the prompts (e.g. jiraiya runs konan → nagato; minato runs rocklee → obito → rocklee).

## The workflow extras (optional)

`docs/agents/` contains a per-repo workflow configuration (local-markdown issue tracker, triage label vocabulary, domain-doc rules) designed for Matt Pocock's engineering skills — copy it only if you use those skills.

## License

MIT
