Synthetic (synthetic.new) reaches fantasy today through catwalk as an `openai-compat` provider, and that works — until a subscription quota runs out. The 429 then in play names no retry moment anywhere in its documented body:

```json
{"error":"You've exceeded your subscription rate limits. Upgrade, or try again later. You can view your usage at https://synthetic.new/billing"}
```

So a quota-locked session just fails with "too many requests" and the user has no idea when to come back. The renewal moment does exist — `GET /v2/quotas` reports `{"subscription":{"limit":135,"requests":0,"renewsAt":"2025-09-21T14:36:14.288Z"}}` and [is documented](https://dev.synthetic.new/docs/synthetic/quotas) not to count against the very limits it reports — but nothing consults it.

## What Changed

New `providers/synthetic` package wrapping `openaicompat`:

- When a request fails on a quota-exhausted 429 (classified by `QuotaExhausted` from #433), the language-model wrapper consults `/v2/quotas` — 5s timeout, endpoint derived from the API origin, `WithQuotaURL` override for proxies — and records `renewsAt` as `ProviderError.RetryAt`, with usage folded into the message: `You've exceeded your subscription rate limits (137 of 135 requests used). ...; retry at 2026-10-25T14:36:14Z (in 23h59m)`.
- Best-effort by design: a failed probe leaves the original error untouched.
- Together with #433's retry middleware this composes into the full behavior: renewal within `MaxRetryDelay` → the middleware waits exactly that long; beyond it → fail fast on the first attempt, naming the moment, instead of hammering a locked door.

Stacked on #433 (uses `RetryAt` and `QuotaExhausted`); the diff below shows both changes until that one merges.

## Evidence

- `go test ./...` — pass (`providers/kronk` panics locally on a missing libffi; environmental)
- `go vet ./...`, `golangci-lint run` — pass
- New coverage, all against an httptest double of both endpoints: the deadline is fetched and carried on the error; the message reports usage; a failed probe leaves the error untouched; quota endpoint URL derivation; and the end-to-end path through `fantasy.RetryWithExponentialBackoffRespectingRetryHeaders` — one attempt, then the error names the renewal moment

Follow-up out of scope here: pointing catwalk's `synthetic` entry at this package so Crush picks it up natively.

- [x] I have read [`CONTRIBUTING.md`](https://github.com/charmbracelet/.github/blob/main/CONTRIBUTING.md).
- [ ] I have created a discussion that was approved by a maintainer (for new features).

💘 Generated with Crush
