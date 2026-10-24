# TODO List

> Fork-work backlog for the charmbracelet/fantasy initiative (rate-limit deadlines + open PRs).
> Canonical location: this jj clone (durable across sessions). The sibling git checkout
> `/home/lars/forks/art-fantasy` was re-cloned on 2026-10-24 and loses uncommitted files.
> Harvested 2026-10-24 from the lost `docs/status/2026-10-24_12-33` report via session
> summary — only its top items are reconstructable; deeper detail died with the re-clone.

## Status legend

| Status           | Meaning                                                 |
| ---------------- | ------------------------------------------------------- |
| 🔴 `TODO`        | Not started. Needs doing.                               |
| 🟡 `IN_PROGRESS` | Actively being worked on.                               |
| 🔵 `BLOCKED`     | External dependency (upstream merge, user decision).    |
| 🟢 `DONE`        | Completed. Remove from this list (logged in the report).|

## High Impact (initiative critical path)

| Task                                                                                          | Status    | Evidence                                                        |
| --------------------------------------------------------------------------------------------- | --------- | --------------------------------------------------------------- |
| Rebase #433/#434 (and stack #421→#420→#422, #417) when main@upstream moves; verify, push       | 🔵 `BLOCKED` | Policy fixed 2026-10-24: immediately if red/conflicted, else weekly; `AGENTS.md` |
| Parser-convergence PR after #433 merges: unify `getRetryDelayInMs` vs `RetryAtFromHeaders` split brain | 🔵 `BLOCKED` | PR #433 branch `retry.go:21`, `errors.go` (two deadline parsers) |
| After merge chain (#433 → #434): crush bumps fantasy, then catwalk switches to deadline-aware retries | 🔵 `BLOCKED` | Chain recorded in `AGENTS.md`; nothing actionable until merges   |
| Post Discussion body when a maintainer asks; locate real PR template first (two guessed paths 404'd) | 🔵 `BLOCKED` | `docs/drafts/discussion-rate-limit-deadlines.md` (staged, voice-checked) |

## Medium Impact (needs explicit user go-ahead — new upstream PRs)

| Task                                                                        | Status    | Evidence                                                 |
| --------------------------------------------------------------------------- | --------- | -------------------------------------------------------- |
| Anthropic `RetryAt` wiring test (openai has one; anthropic only wired)       | 🔴 `TODO` | PR #433 branch `providers/anthropic/error.go` (UNVERIFIED citation) |
| Verify the 2 unverified TODO_LIST citations: anthropic error.go path, `RetryAtFromHeaders` line | 🔴 `TODO` | `grep \| head` truncation fault; report 2026-10-24_17-18 d)4 |
| Shrink art-fantasy `AGENTS.md` fork section to pointer + DERIVED marker (kill split brain) | 🔴 `TODO` | Report 2026-10-24_17-18 e)2 |
| Investigate `.git/info/exclude` local-memory pattern for jj clone (never enters history) | 🔴 `TODO` | Report 2026-10-24_17-18 e)3 |
| Record auto-git daemon truth in AGENTS.md (daemon provably absent in art-fantasy) | 🔵 `BLOCKED` | Report 2026-10-24_17-18 g)2 — needs user answer |
| Synthetic provider tests: options, enrich, timeout paths                    | 🔴 `TODO` | PR #434 branch `providers/synthetic/synthetic.go` (18 funcs, tests thin) |
| Audit `RetryError` deadline-surfacing: does the caller ever see `RetryAt`?  | 🔴 `TODO` | PR #433 branch `errors.go:91` `QuotaExhausted`, `retry.go` |

## Low Impact (local environment)

| Task                                                                          | Status    | Evidence                                          |
| ----------------------------------------------------------------------------- | --------- | ------------------------------------------------- |
| Install golangci-lint 2.15.0 locally (CI parity; local 2.14.0)                | 🔴 `TODO` | CI runs 2.15.0; dupl threshold watch item at #420 |
| Fix rg-hook false positive ("rg -rn replace trap" misfires without `-r`)      | 🔴 `TODO` | Host crush hook config; blocked two greps 2026-10-24 |
| Add UNVERIFIED-marker convention to TODO_LIST evidence columns              | 🔴 `TODO` | Report 2026-10-24_17-18 e)5 |
| Crush session-start hook injecting `date` output (kills timestamp guesses)   | 🔴 `TODO` | Report 2026-10-24_17-18 d)1, f)16 |
