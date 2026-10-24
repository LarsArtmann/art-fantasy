# Fantasy Development Guide

## Commands

- **Build**: `go build ./...`
- **Test all**: `task test` or `go test ./... -count=1 -timeout=30m`
- **Test single**: `go test -run TestName ./package -v`
- **Lint**: `task lint` or `golangci-lint run`
- **Lint fix**: `task lint:fix`
- **Format**: `task fmt` (uses `gofumpt`, not `gofmt`)

## Style Notes

- Prefer `cmp.Or` for defaults
- Struct tags `json`, `description`, `enum` drive schema generation
- `charm.land/x/vcr` for HTTP test recording (not go-vcr)

## Project Layout

- `/` — Core package `fantasy`: Provider, LanguageModel, Agent, Content, Tool, errors, retry
- `/providers/{openai,anthropic,google,bedrock,azure,openrouter,openaicompat,vercel,kronk}`
- `/object` — Typed structured outputs: `object.Generate[T]`, `object.Stream[T]`
- `/schema`, `/jsonrepair` — JSON schema generation and repair utilities
- `/providertests` — Integration tests with VCR cassettes in `testdata/`

## Testing

- Env vars: `FANTASY_<PROVIDER>_API_KEY`, loaded from `.env` via godotenv
- VCR recorder injected as `http.Client` transport

## Local Fork Workflow (fork-only — never into upstream PRs)

This section is a local addition living in the working-copy commit `@`; `@` also holds `docs/drafts/*.md`, `TODO_LIST.md`, and `docs/status/*.md`. Never push `@` blindly and never squash it into PR branches. The sanctioned durability mechanism is the `local-memory` bookmark on `origin` (the fork), run at session end: `jj describe -m "docs: local fork memory snapshot" && jj bookmark set local-memory -r @ && jj git push --bookmark local-memory`.

- Remotes: `origin` = LarsArtmann/art-fantasy (fork, PR branches), `upstream` = charmbracelet/fantasy. Fetch at every session start: `jj git fetch --all-remotes`.
- Open PRs (all green, rebased onto main 28d60cb0 on 2026-10-24): #417 `object-parse-failure-error-type`; stack #421 `characterization-tests` → #420 `enable-dupl-linter` → #422 `dedupe-test-suites`; #433 `push-xlrnwtqnkpyk` (rate-limit retry deadlines) → #434 `push-nsoqquqxynyp` (synthetic provider).
- Rebase a whole branch family: `jj rebase -b <bookmark> -d main@upstream`; verify locally (build/vet/test/lint) before pushing; `jj git push --deleted` cannot combine with `--bookmark`.
- Rebase policy (2026-10-24): immediately when red/conflicted; else at most weekly or when pushing new commits (govulncheck drift once left the stack red for 4 days; green force-pushes are pure churn).
- Ping policy (2026-10-24): stay silent on unreviewed green PRs — pings are off-norm and ineffective here (#359 pinged 3×, still waiting; #400 merged day 14 unprompted). Revisit ~2026-10-31.
- Rate-limit initiative merge chain: #433 → #434 → crush bumps fantasy → catwalk switches. Discussion draft staged in `docs/drafts/discussion-rate-limit-deadlines.md` — post only when a maintainer asks.
- Verify tips without disturbing bookmarks: `jj new <tip> -m temp` … `jj edit ozzzwsst` (or current `@`) afterwards; abandon temp changes.
- kronk tests panic locally on missing libffi.so.8 (environmental); prove compile with `go test -c -o /dev/null ./providers/kronk`.
- The sibling git checkout `/home/lars/forks/art-fantasy` is read/research only for code; it was re-cloned 2026-10-24 and loses uncommitted files, so this file is the durable copy of fork memory.
