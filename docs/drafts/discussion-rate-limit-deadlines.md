Subscription quotas answer 429 with a plain "rate limit exceeded" and no retry moment. fantasy's middleware then spends its retry budget (3 attempts, 5/10/20s backoff) against a wall that renews on a billing schedule, and the caller is left with "too many requests" and no answer to the only question that matters: when can a retry actually succeed?

## Goal

As a fantasy user hitting a quota 429, I want the error to name the moment the quota renews, and I want the retry loop to either wait for that moment (when it fits the budget) or stop immediately instead of retrying into a locked door.

|                        | today              | desired                                               |
| ---------------------- | ------------------ | ----------------------------------------------------- |
| 429 with `retry-after` | capped at 60s      | wait until the deadline, within a configurable budget |
| quota 429, no deadline | 3 futile retries   | fail fast, not retryable                              |
| final error message    | "too many requests" | `...; retry at 2026-10-25T14:36Z (in 23h59m)`         |

Synthetic is the concrete case that motivated this: its quota 429 names no retry moment anywhere in the response, but `GET /v2/quotas` (documented not to count against the subscription) returns the renewal time.

Two PRs implement it, happy to restructure if you would rather land this via Discussion first:

- #433 — typed `RetryAt`/`QuotaExhausted` on `ProviderError`, standard `retry-after` header parsing, wait-until-deadline within a `MaxRetryDelay` budget, fail-fast beyond it. No behavior change for responses without deadlines.
- #434 — `providers/synthetic` wrapping `openaicompat`, probing `/v2/quotas` on quota errors to fill the renewal deadline and usage into the error. All synthetic-specific knowledge stays in that package.

The seam is `ProviderError` plus `getRetryDelayInMs`; the quota probe is opt-in per provider.

💘 Generated with Crush
