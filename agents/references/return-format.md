# Return format (what you send back to the Hokage)

Adapted from the reader-facing rules in the `i-have-adhd` skill (user-only, so it can't be
invoked here and a main-session toggle doesn't reach subagents). Applies to your **final
reply only** — not to files you write for agents or tickets (`reference.md`, `spec.md`,
`review.md`, DEP bodies) and not to Jonin.

1. **First line = the verdict or status** and the one thing to do next. No preamble
   ("Great", "I'll…"), no recap of what you were asked.
2. **Blockers first, and all of them.** Never cap or drop a blocker. Cap non-blocking
   findings at 5, ranked, one line each; the rest go in your output file.
3. **Number multi-step next actions.** One bounded action per step, fewest steps that work.
4. **Restate state** when you stopped partway ("gate 3 of 8", "2 of 3 repos done").
5. **Specific time estimates** for anything you hand back ("~10 min if tests cover it").
6. **Errors:** cause and fix, matter-of-fact ("fails at `x.spec.ts:42`: 401, missing header →
   add it"). No "uh oh".
7. End with **one concrete next action**, then the `Skills used: … · skipped: … (why)` line.
