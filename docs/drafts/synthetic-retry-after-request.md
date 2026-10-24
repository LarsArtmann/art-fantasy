# Draft: feedback to Synthetic (send via https://synthetic.new/discord or the contact email from dev.synthetic.new)

Subject: Rate limit 429s: please include the renewal moment (Retry-After or renewsAt)

Hi Synthetic team,

When a subscription quota is exhausted, the API answers 429 with a body that names no retry moment:

    {"error":"You've exceeded your subscription rate limits. Upgrade, or try again later. You can view your usage at https://synthetic.new/billing"}

The renewal time is available at GET /v2/quotas (`renewsAt`), but a client hitting the 429 has to know that endpoint exists to find it. Would you consider putting the retry moment on the 429 itself — a standard `Retry-After` header (delay-seconds or HTTP-date), or the `renewsAt` timestamp inside the error body? Standard headers would let the retry logic most OpenAI-compatible clients already ship do the right thing with zero integration work.

Context: I sent charmbracelet/fantasy PRs #433 and #434, where the SDK now queries /v2/quotas on a quota 429 to fill exactly this gap. Happy to test whatever format you pick.

Thanks!
Lars
