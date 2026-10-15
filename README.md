# The Hokage-Shinobi-Jonin agent system

There is no orchestrator skill here — the **Hokage** (you, the developer)
calls each **Shinobi** directly, by name, through the `Agent` tool
(`subagent_type: "<name>"`). Each Shinobi commands a **fixed squad of 2
Jonin** (matched to [vivek-baliyan/shinobi-agents](https://github.com/vivek-baliyan/shinobi-agents)'
roster and squad assignments) — not a shared pool. Every check this project
has learned the hard way is kept (see "What's ours, not upstream's" below).

## The 8 Shinobi

| Shinobi | Role | Squad (fixed, 2 Jonin) |
|---|---|---|
| [kakashi.md](kakashi.md) | Codebase recon & briefings; existing-ticket triage | neji, hinata |
| [tobirama.md](tobirama.md) | Requirements & planning — spec (new feature) or implementation plan (existing ticket), plus its own plan-quality self-check | ino, shikamaru |
| [hashirama.md](hashirama.md) | Fast, full-scope implementation — may leave bugs on purpose | yamato, naruto |
| [tsunade.md](tsunade.md) | Debugging & root-cause fixes; fresh-eyes bugfix + test coverage on hashirama's diff | shizune, sakura |
| [orochimaru.md](orochimaru.md) | Adversarial QA & edge cases — a real end-to-end scenario trace, not diff-scoped | kabuto, deidara |
| [itachi.md](itachi.md) | Code review & security — `BLOCKING`/`NON-BLOCKING` gate | shisui, sasuke |
| [jiraiya.md](jiraiya.md) | External research against primary sources — on-demand, not in the mandatory chain | konan, nagato |
| [minato.md](minato.md) | Performance optimization — on-demand, not in the mandatory chain | rocklee, obito |


## docs/: the only non-code source of truth

For any question about what the system currently does, the only two valid
sources are the code itself, read fresh, and `docs/` at the workspace
root. `eva-docs/`, a repo's own scattered prose, or a `CONTEXT.md`/
`docs/adr/` that may or may not exist in a given repo are not cited or
trusted by any Shinobi or Jonin here — see `docs/README.md` for the lane
table and `references/docs-instance-convention.md` for the mechanic. Lanes:
`docs/feature/` (kakashi `reference.md` + tobirama `spec.md`/`plan.md`),
`docs/plans/` (tobirama `plan.md` for planning that is not a feature),
`docs/pr-review/` (itachi `review.md`, orochimaru `findings.md` — final
output only, deleted by hand), `docs/optimizations/` (minato `plan.md`),
and `docs/qa-release/QA_RELEASE.md` (the release process). Instance folders
are `<YYYY-MM-DD>-<slug>/`. There is **no index**: to find prior work, `ls`
the lane.

## Shared references

- [references/docs-instance-convention.md](references/docs-instance-convention.md) — where each lane's output goes.
- [references/skills-map.md](references/skills-map.md) — which skills each Shinobi must invoke (and which can't run in a subagent).
- [references/return-format.md](references/return-format.md) — how a Shinobi's final reply to the Hokage is shaped.

## The Jonin squads (16, fixed — 2 per Shinobi)

Each Jonin belongs to exactly one Shinobi's squad (its `squad_of` field);
dispatched only by that Shinobi, via the Agent tool with
`subagent_type: <jonin-name>`, for the subtask matching its specialty —
pick the one whose trait fits, don't default to convenience:

| Jonin | Squad of | Specialty | File |
|---|---|---|---|
| neji | kakashi | Structure/dependency sweeps across every target in one pass | [jonin/neji.md](jonin/neji.md) |
| hinata | kakashi | Usage lookups with citations, every call site | [jonin/hinata.md](jonin/hinata.md) |
| ino | tobirama | One-page decision briefs | [jonin/ino.md](jonin/ino.md) |
| shikamaru | tobirama | Plan-quality self-check (the old top-tier Kakashi checklist lives here now) | [jonin/shikamaru.md](jonin/shikamaru.md) |
| yamato | hashirama | Structural builds — scaffolding, seam wiring | [jonin/yamato.md](jonin/yamato.md) |
| naruto | hashirama | Volume pattern builds — same-shape change, many call sites | [jonin/naruto.md](jonin/naruto.md) |
| shizune | tsunade | Deterministic reproductions | [jonin/shizune.md](jonin/shizune.md) |
| sakura | tsunade | Root-cause diagnosis chains | [jonin/sakura.md](jonin/sakura.md) |
| kabuto | orochimaru | Adversarial test design | [jonin/kabuto.md](jonin/kabuto.md) |
| deidara | orochimaru | Stress runs, failure injection | [jonin/deidara.md](jonin/deidara.md) |
| shisui | itachi | Spec-fidelity — exact match, claimed-wiring traces | [jonin/shisui.md](jonin/shisui.md) |
| sasuke | itachi | Security — injection/IDOR/authz/trust boundaries | [jonin/sasuke.md](jonin/sasuke.md) |
| konan | jiraiya | Raw source gathering | [jonin/konan.md](jonin/konan.md) |
| nagato | jiraiya | Verification & conflict resolution | [jonin/nagato.md](jonin/nagato.md) |
| rocklee | minato | Benchmarking with repeats/variance | [jonin/rocklee.md](jonin/rocklee.md) |
| obito | minato | Profile analysis, cost ranking | [jonin/obito.md](jonin/obito.md) |

### Shared rules (every Jonin)

Every Jonin file below assumes this block instead of restating it:

Dispatched only by its own Shinobi, for the subtask matching its specialty.
Flag anything used that isn't written into its file yet, in what it returns —
that's how these files grow, as a Hokage-reviewed edit, not something a
Jonin rewrites itself mid-task.

Rules in each Jonin's file are durable conventions. Inline examples/line
numbers are illustrative snapshots — re-verify against current code before
citing them as still true.

**What no Jonin does:**
- Hold forth on a repo outside its three scoped repos (where scoped —
  jiraiya's squad works external sources instead).
- Cite `eva-docs/` or any prose doc outside `docs/` as fact — code, freshly
  read, and `docs/` are the only sources.
- Edit its own file.
- **Spawn a subagent** — platform-enforced via
  `CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH=2` in `.claude/settings.json`, not
  just this rule. A Jonin has no `Agent` tool call to make in the first
  place; the env var is the hard guarantee, this line is the stated one.

## Two lanes, symmetric approval gates

- **Plan lane** (before code exists): kakashi (recon; existing-ticket
  triage) → tobirama (spec/plan + its own plan-quality self-check) →
  Hokage approve → Hokage publishes to Linear. Jiraiya dispatched on-demand by
  kakashi/tobirama for external-fact verification. Minato dispatched
  on-demand when a perf concern surfaces.
- **Build lane** (after a sound plan/ticket): hashirama → tsunade → Hokage
  approve → orochimaru (adversarial trace) → Hokage approve → itachi
  (review) → Hokage lands → Hokage closes the ticket. Minato dispatched on-demand when a
  perf concern surfaces.

Every stage states a verdict first (`SOUND`/`NEEDS REVISION`,
`fine`/`not-fine`, `TASKS COMPLETE`/`NOT COMPLETE`, `HOLDS`/`BREAKS`,
`BLOCKING`/`NON-BLOCKING`) and an explicit next step in its own reply — no
step advances on inferred consent, no gate needed to send work back.

### Trivial-fix fast path

For a genuinely tiny fix — a typo, a one-line guard, a copy change with no
new behavior — the Hokage may skip kakashi/tobirama planning and
orochimaru's adversarial pass: straight `hashirama → tsunade → Hokage
approve → itachi → Hokage lands → Hokage closes the ticket`. This is the Hokage's own
judgment call, not something any Shinobi decides for itself — when in
doubt, take the full lane.

## Maintenance: editing a Shinobi's own file

When you (the Hokage) hand-edit one of these `.md` files to fix a problem —
a wrong trigger, a missing instruction, or the file drifting back toward
the bulk this system replaced — apply `superpowers:writing-skills`' own
RED/GREEN/REFACTOR discipline to the edit itself:

1. **RED** — confirm what's actually going wrong before touching anything
   (a real observed failure, not a guess).
2. **GREEN** — make the smallest edit that fixes it. Don't pad.
3. **REFACTOR** — check the new wording doesn't hand the agent an obvious
   rationalization to skip it (vague language, an escape hatch, a
   contradiction with an existing "What you don't do" bullet).

This applies to editing the files themselves — it's not something any
Shinobi invokes on itself at runtime (the one exception is tobirama, which
runs this same discipline against its own spec/plan draft before handing
off, since a published ticket has to work the same way a skill file does:
a zero-context reader has to execute it correctly).

The same discipline covers `.claude/agents/<legend>/jonin/*.md` — a persona file
only gains a new line from a real observed mistake in one of its scoped
repos, triggered by that persona's own "flag it in what you return" step,
never a speculative addition.
