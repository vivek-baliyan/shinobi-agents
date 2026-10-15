# Skills map

Read this before your first step. Invoke every **REQUIRED** skill for your role with the
Skill tool, and end your summary with `Skills used: … · skipped: … (why)`.

Pass bar for REQUIRED: runs headless in a subagent (no user to answer), no session hooks,
no browse daemon, no commits by default. Verified 2026-10-13 by loading each skill from a
subagent. Subagents have no `AskUserQuestion`: when a skill wants an answer, return the
question to the Hokage instead of stalling.

| Role | REQUIRED | OPTIONAL (with the caveat to apply) |
|---|---|---|
| kakashi | `graphify` — `graphify query "<q>"` against `graphify-out/graph.json` first, then verify in code | — |
| tobirama | `mattpocock-skills:writing-for-agents` | `mattpocock-skills:grilling` (return the first round of questions + your recommended answers; the loop can't finish here), `codebase-design`, `superpowers:writing-plans` (plans lane: return inline, no file, drop commit steps), `ponytail:ponytail-review` on a draft |
| hashirama | `superpowers:test-driven-development`, `superpowers:verification-before-completion` | `mattpocock-skills:tdd` (seams must be pre-agreed in the brief), `design-taste-frontend` and `ui-ux-pro-max:ui-ux-pro-max` for UI, `mattpocock-skills:resolving-merge-conflicts` |
| tsunade | `superpowers:systematic-debugging` (3 failed fixes → stop and report), `mattpocock-skills:diagnosing-bugs` (no feedback loop → report back), `superpowers:verification-before-completion` | `mattpocock-skills:tdd` |
| orochimaru | — | `mattpocock-skills:prototype` (skip its commit/issue step) |
| itachi | `ponytail:ponytail-review`, `mattpocock-skills:code-review` (pass the fixed point + spec path; run in a sub-repo, the workspace root isn't git) | — |
| jiraiya | `mattpocock-skills:research` — say "return findings inline, no file" | — |
| minato | `mattpocock-skills:diagnosing-bugs` | — |

## Not for agents (they stay installed — use them in the main session)

- **Write files / commit by default, or need approval:** `mattpocock-skills:domain-modeling`
  (writes `CONTEXT.md`/ADRs), `superpowers:brainstorming` (writes and commits
  `docs/superpowers/specs`) — borrow their thinking, write nothing; `superpowers:using-git-worktrees`
  (you prefer branches).
- **User-only (`disable-model-invocation`), the Skill tool refuses them:** `mattpocock-skills:implement`
  (inlined in hashirama), `to-spec`, `to-tickets`, `triage`, `grill-with-docs`,
  `improve-codebase-architecture`, `wayfinder`, `handoff`, `i-have-adhd`.
- **gstack — fails the headless bar** (needs `AskUserQuestion`, the browse daemon, or scope-lock
  hooks; many edit code, commit, push, or write `~/.gstack` and repo report dirs): `investigate`,
  `qa-only`, `qa`, `review`, `cso`, `benchmark`, `careful`, `freeze`, `guard`, `diagram`,
  `plan-eng-review`, `plan-design-review`, `design-review`, `health`, `document-release`,
  `context-save`, `ship`, `land-and-deploy`, `canary`, `autoplan`, `retro`, `codex`, `office-hours`,
  `learn`. Their agent-safe equivalents are above (`systematic-debugging`, `code-review`, …).
- `ponytail:ponytail-audit`, `i-have-adhd`, `caveman`, `ars-*`/`deep-research`, `last30days`:
  Hokage-only or off-domain.
