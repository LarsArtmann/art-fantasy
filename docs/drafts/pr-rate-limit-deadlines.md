When a provider answers 429 with a `retry-after` beyond 60 seconds, the retry middleware silently discards it: `getRetryDelayInMs` treats any header delay at or beyond a hardcoded 60s cap as unreasonable, falls back to exponential backoff, and the error a caller finally receives says "too many requests" with no hint of when a retry can actually succeed.

Subscription quotas are the worst case. They renew on a billing schedule — hours or days out — so the middleware burns its whole retry budget against a locked door and then reports nothing actionable. Synthetic's quota-exhausted 429 (body: `{"error":"You've exceeded your subscription rate limits. Upgrade, or try again later. ..."}`) is the concrete case that motivated this; nothing in the response names a retry moment.

## What changed

- `ProviderError.RetryAt`: the earliest retry moment a provider declared, parsed from `retry-after` (delay-seconds or HTTP-date) and `retry-after-ms` by the new `fantasy.RetryAtFromHeaders`, case-insensitively. Wired into the openai (incl. openai-compat) and anthropic error constructors. `Error()` appends `; retry at 2026-10-25T14:36:14Z (in 23h59m)` when a future deadline is known, so even a giving-up caller learns when to come back.
- `ProviderError.QuotaExhausted`: marks a billing/subscription quota hit, set via `fantasy.IsQuotaExhaustedMessage` (matches "exceeded your subscription"; per-minute throttles say "rate limit" without it and keep their classification). A quota exhaustion with no known deadline reports `IsRetryable() == false`: no automatic budget can outwait a billing cycle.
- `RetryOptions.MaxRetryDelay` (default 60s, the previous hardcoded cap): bounds server-declared waits. Within it, the middleware waits exactly until `RetryAt` instead of guessing. Beyond it, a plain rate limit keeps today's backoff behavior, while a quota exhaustion fails fast with the original error naming the renewal moment instead of burning retries that cannot succeed.

Defaults are behavior-identical to today: no deadline header and no quota marker means the same backoff and the same error strings.

## Evidence

- `go test ./...` — pass (`providers/kronk` panics locally on a missing libffi; environmental, it still compiles via `go test -c`)
- `go vet ./...` — pass
- `golangci-lint run` — pass
- New coverage: header parsing (delay-seconds, HTTP-date, ms precedence, case-insensitive names, invalid/zero/negative/past values); quota-body classification through a real round trip against the openai provider; all four retry-middleware deadline paths (no deadline, deadline beyond budget, deadline within budget, non-quota header hint beyond budget)

- [x] I have read [`CONTRIBUTING.md`](https://github.com/charmbracelet/.github/blob/main/CONTRIBUTING.md).
- [ ] I have created a discussion that was approved by a maintainer (for new features).

Happy to move this to a Discussion first if you'd rather shape the API there — filing as a PR because it is test-covered end to end and motivated by a concrete failure mode.

💘 Generated with Crush
