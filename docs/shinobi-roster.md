# Shinobi Roster — Agent Reference

The Hidden Leaf command structure for this workspace. 8 legendary shinobi (dispatched by you, the Hokage), each commanding a squad of max 2 jonin. Every report ends with **Next shinobi:** — a handoff recommendation; you always do the actual dispatch.

- Legendaries: `model: inherit` — full session capability.
- Jonin: `model: sonnet` — focused, cheaper strikes. Also directly dispatchable when one focused strike is enough.
- Squad limits: depth hard-capped at 2 (`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH` in `.claude/settings.json`); jonin have no `Agent` tool and can never spawn further.
- Definitions live in `.claude/agents/*.md`.

## The lifecycle flow

```mermaid
flowchart TD
    HOKAGE["HOKAGE (you)"]
    K["kakashi — recon"]
    T["tobirama — plan"]
    HA["hashirama — build"]
    I["itachi — review"]

    HOKAGE -->|"1. dispatch"| K
    K -->|"2. intel brief"| T
    T -->|"3. mission plan"| HA
    HA -->|"4. built code"| I
    I -->|"5. approved"| HOKAGE

    J["jiraiya — external research"]
    TS["tsunade — debug & fix"]
    O["orochimaru — adversarial QA"]
    M["minato — performance"]

    J -.->|"facts"| K
    J -.-> T
    O -.->|"after build"| HA
    I -->|"findings"| TS
    O -->|"weaknesses"| TS
    TS -->|"fixed code"| I
    M -.->|"any stage"| HA
    M -->|"optimized code"| I
```

**How a mission runs:**

1. **kakashi** scouts the codebase and returns an intel brief.
2. **tobirama** turns the requirement into a refined, ordered mission plan.
3. **hashirama** builds it, tests green.
4. **itachi** reviews the result; **orochimaru** tries to break it.
5. **itachi** approves → back to you, the Hokage, to ship.

**Support shinobi, called when needed:**
- **jiraiya** — the answer lives outside the repo (libraries, APIs, upstream bugs).
- **tsunade** — itachi or orochimaru found something broken; she fixes it and returns it to review.
- **minato** — something is measurably slow; he optimizes with before/after numbers, then review.

Squads stay off this map — each legendary carries his own 2 jonin (see per-agent sections below).

---

## Legendaries

### kakashi — recon & intel briefings
- **Work:** Maps a target area end-to-end: entry points, data flow, conventions, gotchas — every claim cited `path:line`. Copies the codebase's existing patterns instead of inventing new ones.
- **In the flow:** First move on any mission. Produces the intel brief that tobirama plans from. Hands external-docs questions to jiraiya.
- **Squad:** `neji` (breadth — structure sweeps) + `hinata` (depth — precise lookups); dispatches both in parallel for independent questions.
- **Handoffs:** → tobirama (intel ready to plan), → jiraiya (question leaves the repo).

### jiraiya — deep external research
- **Work:** Researches libraries, APIs, version compatibility, upstream bugs against primary sources (docs, changelogs, source). Checks versions against the repo's actual package files. Findings can be captured as a cited markdown file.
- **In the flow:** Any time the answer isn't in the village. Feeds verified facts into kakashi's briefs or tobirama's plans.
- **Squad:** `konan` (gathers raw sources) → `nagato` (synthesizes into one verified answer) — strict order, synthesis needs the material.
- **Handoffs:** → tobirama (research informs the plan), → kakashi (facts needed inside the repo).

### tobirama — requirements refinement & mission planning
- **Work:** Interrogates requirements until acceptance criteria are unambiguous, cuts scope that doesn't serve the goal, and produces ordered, file-exact mission plans executable without further questions.
- **In the flow:** After recon, before any build. The gate between "idea" and "mission".
- **Squad:** `ino` (condenses raw intel into a one-page brief) → `shikamaru` (drafts the ordered task plan) — order matters.
- **Handoffs:** → hashirama (plan approved), → jiraiya (open question needs research).

### hashirama — feature implementation
- **Work:** Builds the plan: new features, modules, UI — in small vertical slices, matching existing conventions exactly. All tests green before reporting. Ponytail discipline: minimum code that works.
- **In the flow:** The build phase. Executes tobirama's missions; his output goes to itachi.
- **Squad:** `yamato` (structural multi-file work) + `naruto` (volume — many similar pieces). Splits the plan by shape; parallel when independent.
- **Handoffs:** → itachi (build done, review it), → tsunade (breaks found during build).

### tsunade — debugging & root-cause fixes
- **Work:** Reproduces, traces to the true root cause (never the first suspicious line), fixes with the minimal diff covering ALL callers, adds the regression test. No refactors bundled into fixes.
- **In the flow:** Whenever anything breaks — during build, after review findings, or on production smells.
- **Squad:** `shizune` (deterministic repro + evidence) → `sakura` (root cause + blast radius). Clear repro in hand → sakura directly.
- **Handoffs:** → itachi (fix needs review), → minato (the bug is a perf regression).

### itachi — code review & security
- **Work:** Reviews diffs on four axes: correctness, security (injection, authz gaps, trust boundaries between web and API), spec fidelity, maintainability. Ranked findings with failure scenarios; explicit about what was checked and is clean.
- **In the flow:** The quality gate after every build (and after tsunade's fixes). Verdicts: approve / approve-with-fixes / reject.
- **Squad:** `shisui` (spec fidelity — exactly what was asked) + `sasuke` (security — vulnerability hunt). Both run on the same diff in parallel.
- **Handoffs:** → tsunade (findings need fixing), → hokage (approved, ship).

### orochimaru — adversarial QA & edge-case experiments
- **Work:** Designs and runs the experiments nobody else dares: hostile inputs, boundary values, concurrency, failure injection. Every discovered weakness becomes a passing test. No debris left behind.
- **In the flow:** After hashirama's build (alongside or after itachi) when confidence must be earned the hard way.
- **Squad:** `kabuto` (experiment matrix + encoding findings as tests) · `deidara` (destructive stress runs, instrumented).
- **Handoffs:** → tsunade (weaknesses need fixes), → hashirama (tests demand implementation changes).

### minato — performance optimization
- **Work:** Baseline measurement → dominant cost identified with evidence → smallest change that removes it → re-measure. No optimization without before/after numbers; behavior preserved.
- **In the flow:** Any stage where something is measurably slow — API latency, bundle size, query plans, render cost.
- **Squad:** `rocklee` (repeatable benchmarks) ⇄ `obito` (profile interpretation). Measure → interpret → strike → re-measure.
- **Handoffs:** → tsunade (optimization exposed a bug), → itachi (change needs review).

---

## Jonin squads (directly dispatchable too)

Each jonin serves one legendary but can be summoned directly by the Hokage for a single focused strike.

| Jonin | Squad | Tools | Work |
|---|---|---|---|
| `neji` | kakashi | read-only | 360° structure sweeps: module map, layering, dependency direction, tangles. |
| `hinata` | kakashi | read-only | Gentle Fist lookups: exact usages, callers, definitions — cited or honestly "not found". |
| `konan` | jiraiya | read + web | Gathers raw sources (docs, changelogs, release notes, issues) with URLs and excerpts. No opinions. |
| `nagato` | jiraiya | read + web | Reconciles gathered sources into one verified answer with per-claim confidence; resolves conflicts. |
| `ino` | tobirama | read-only | Mind Transfer relay: compresses recon/research reports into a one-page, decision-ready brief. |
| `shikamaru` | tobirama | read-only | Drafts ordered task plans from refined requirements; finds the lazy path; flags open questions. |
| `yamato` | hashirama | full | Structural builds: new modules, interfaces, layer-spanning wiring — blueprint discipline. |
| `naruto` | hashirama | full | Volume builds: many similar pieces (endpoints, components, tests) ground out via one consistent pattern. |
| `shizune` | tsunade | full | Deterministic reproductions: exact steps, failing tests, logs — flakiness isolated to 100%. |
| `sakura` | tsunade | read-only | Root-cause diagnosis: evidence chain to the exact faulty line, plus blast radius of every caller. |
| `shisui` | itachi | read-only | Spec-fidelity review: every requirement implemented, nothing extra — gaps and additions reported separately. |
| `sasuke` | itachi | read-only | Security: injection, IDOR/authz gaps, input validation, secret leakage at web⇄API trust boundaries. |
| `kabuto` | orochimaru | full | Experiment design: adversarial test matrix; encodes every discovered weakness as a test. |
| `deidara` | orochimaru | full | Executes stress runs (load, chaos, hostile payloads); reports exactly what broke and why. Art is an explosion. |
| `rocklee` | minato | full | Benchmarks: baseline/after numbers with method, repeats, and variance. Honest numbers only. |
| `obito` | minato | read-only | Profile analysis: ranks costs from profiler/timing/bundle data, names the dominant one with attribution. |

## Skill wiring (quick reference)

| Shinobi | Superpowers | Mattpocock | Ponytail |
|---|---|---|---|
| kakashi | — | — (hands external work to jiraiya) | — |
| jiraiya | — | `research`, `grilling` | — |
| tobirama | `brainstorming`, `writing-plans` | `grill-with-docs`, `to-spec`, `to-tickets` | — |
| hashirama | `test-driven-development`, `executing-plans` | `implement`, `tdd`, `codebase-design` | always-on |
| tsunade | `systematic-debugging` | `diagnosing-bugs` | — |
| itachi | `verification-before-completion` | `code-review` | review lens |
| orochimaru | `test-driven-development` | `tdd`, `prototype` | — |
| minato | `systematic-debugging` | `diagnosing-bugs` | review lens |

Jonin inherit their legendary's discipline; kabuto/naruto also carry TDD, shikamaru carries ponytail thinking.
