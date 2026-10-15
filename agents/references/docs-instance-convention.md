# Docs convention

Shared by every Shinobi that writes under `docs/`. Each agent's own file says
which lane it writes to; this file is the one place for the mechanic.

| Lane | Folder | Written by | Files |
|---|---|---|---|
| Feature | `docs/feature/<date>-<slug>/` | kakashi, tobirama | `reference.md` (kakashi's fresh code-state recon) + `spec.md` or `plan.md` (tobirama) |
| Plans | `docs/plans/<date>-<slug>/` | tobirama | `plan.md` — planning that is not a feature; no `reference.md` |
| PR review | `docs/pr-review/<date>-<slug>/` and `...-adversarial/` | itachi, orochimaru | `review.md` / `findings.md` — **final output only** |
| Optimizations | `docs/optimizations/<date>-<slug>/` | minato | `plan.md` (measurement, baseline, change, after numbers) |

Rules:

- `<date>` is `YYYY-MM-DD`; `<slug>` is short kebab-case, prefixed with the
  ticket ID when one exists.
- **No index.** To find prior work, `ls` the lane. Don't add rows anywhere.
- **No cleanup.** Never delete another instance's folder. `pr-review/` is
  disposable; the Hokage deletes it by hand.
- `reference.md` exists only in `docs/feature/`. Generate it fresh from the
  code as it is now; never reuse an older one. A citation in it is only as
  good as the moment it was written — re-verify against live code.
- Write the final output only: no drafts, no per-pass copies. A re-run for
  the same diff or ticket overwrites its own file.
- Downstream agents read the folder path they were handed (hashirama,
  itachi), not a guessed `<slug>`.
