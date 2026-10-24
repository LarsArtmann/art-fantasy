# Status 2026-10-24 (afternoon) — decisions made, memory recovered after re-clone wipe

## a) State of the six PRs (verified 2026-10-24 PM)

All six open PRs pass every CI check (lint, build ×3, govulncheck, triage): #417, #420, #421, #422, #433, #434. Zero maintainer comments/reviews on any of them. `main@upstream` unchanged at 28d60cb0 (#432) — no rebase required; the standing session-start fetch confirmed nothing moved.

## b) The three open questions — decided with evidence

1. **Ping maintainers? NO — stay silent.** Evidence: pings are off-norm and ineffective in this repo. #359 pinged three times and has waited since 2026-09-11; #400/#399 merged at day 14 with zero pings; our own #419 merged same-day and #423 in 3 days, unprompted. ~20 community PRs older than ours are still open — we are in a normal queue, not being skipped. Revisit ~2026-10-31 if still unreviewed.
2. **Probe host / Synthetic register — user-only, still blocked.** No synthetic key on this host (`/run/secrets/synthetic_api_key` absent). Probe command lives in `AGENTS.md`. Synthetic feedback draft (`docs/drafts/synthetic-retry-after-request.md`) still unsent — Discord/email are user actions.
3. **Auto-rebase policy — reactive, bounded.** Rebase immediately when any check is red or a PR conflicts (the govulncheck drift incident left the stack red 4 days); otherwise at most weekly or when pushing new commits. Frequent force-pushes of green PRs are pure churn. Policy recorded in both AGENTS.md copies.

## c) Incident: art-fantasy checkout was re-cloned between sessions

The git checkout `/home/lars/forks/art-fantasy` was re-cloned from upstream (reflog shows only clone + rebase onto 28d60cb). This destroyed the prior session's uncommitted files: the AGENTS.md workflow bullets and `docs/status/2026-10-24_12-33_six-prs-rebased-all-green.md` (including its 45-item backlog — only the top ~10 items were reconstructable from the session summary). The jj clone `/home/lars/forks/fantasy` survived fully intact (all bookmarks verified at their post-rebase commits; drafts present).

**New durability layout (implemented):**
- Canonical fork memory: `/home/lars/forks/fantasy/AGENTS.md` (local section, lives in `@`, never pushed).
- Canonical backlog: `/home/lars/forks/fantasy/TODO_LIST.md` (docs-health HARVEST output).
- `art-fantasy/AGENTS.md` carries a rebuilt copy + pointer; it must be assumed volatile.

## d) Done this session

- Verified all six PRs green, zero feedback, main unchanged; fetched upstream (standing rule).
- Decided and documented Q1 (silence), Q3 (reactive rebase), re-confirmed Q2 blocked/user-only.
- Rebuilt `art-fantasy/AGENTS.md` fork-workflow section (PR map, rebase mechanics, policies, probe command, merge chain).
- Created jj-clone `AGENTS.md` local section; created `TODO_LIST.md` (4 blocked / 3 code / 2 env items).

## e) Next steps

- Monitor #433/#434 for maintainer feedback; respond in terse github-voice register when it arrives.
- When maintainer asks: post Discussion draft; tick PR checkboxes (find the real PR template first).
- When main moves or a check goes red: apply the rebase policy (`jj rebase -b <bookmark> -d main@upstream`).
- User-only: send Synthetic draft; run the 429 probe on a key host and update the UNVERIFIED Retry-After caveat.
- Start the three code TODOs (anthropic RetryAt test, synthetic tests, RetryError audit) only on explicit instruction.
