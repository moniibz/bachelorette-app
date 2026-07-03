# Free-Tier Guardrails

This project is configured with a hard-stop posture where the provider allows it, and alert-only monitoring where the provider does not expose a hard cap.

## Google Cloud

- Project: `project-e82df01d-c626-4262-907`
- Billing account: `0124C8-9890CA-7C1BA2`
- Enabled Maps and Calendar APIs were left enabled.
- Maps-related daily quota caps were set to `322` requests per day where Google exposes daily quotas. This keeps each capped API/SKU under `10,000` monthly calls in a 31-day month.
- Calendar API was rate-limited by request quota instead of spend:
  - `100` requests per minute per project
  - `30` requests per minute per user
- Billing budget:
  - Name: `bachelorette-app hard-stop alerts`
  - Amount: `$1/month`
  - Alerts: `50%`, `80%`, `100%`
  - Recipients: Monica and Murad via Google Cloud Monitoring email notification channels
- Maps quota alert policies:
  - `bachelorette-app Maps quota 50 percent`
  - `bachelorette-app Maps quota 80 percent`
  - `bachelorette-app Maps quota 100 percent`

Google Cloud budgets are alerts, not automatic spend stops. The hard-stop layer for Maps is the quota cap.

## Cloudflare R2

- Bucket: `bachelorette-app`
- Storage class: Standard
- Public `r2.dev` access: disabled
- Current stored objects at setup: `0`
- Current stored bytes at setup: `0`

R2 guardrail values are stored locally in `.env.local` and in Vercel environment variables:

- `R2_BUCKET_NAME=bachelorette-app`
- `R2_UPLOAD_MAX_BYTES=10485760`
- `R2_STORAGE_HARD_CAP_BYTES=10200547328`
- `R2_FREE_TIER_CLASS_A_OPS_MONTHLY=1000000`
- `R2_FREE_TIER_CLASS_B_OPS_MONTHLY=10000000`

Future guest uploads must go through a server-side upload gate before any R2 write:

- Reject files over `10 MB`.
- Reject new uploads once stored objects are at or above `9.5 GiB`.
- Never expose permanent R2 write credentials to frontend code.

Cloudflare budget alerts are informational and do not pause usage, so the upload gate is the hard stop.

## Vercel

- Project remains on free/Hobby behavior.
- Google and R2 guardrail environment variables are present in Production, Preview, and Development.
- Do not enable paid add-ons or Pro-only features.
- Spend Management is only available on Pro. If the account is upgraded later, immediately set the lowest acceptable spend threshold and enable production pause.

## Convex

- Convex is intentionally not wired into the app yet.
- Keep the team/project on Free while there is no concrete shared-state feature.
- If upgraded to a paid Convex plan, set the spending disable threshold to `$0/month` beyond included resources.

## GitHub

- Repository: `moniibz/bachelorette-app`
- Visibility: public
- GitHub Actions: disabled
- Murad remains an admin collaborator.

For personal GitHub accounts, budgets and included-usage alerts are managed from the GitHub billing dashboard. Keep metered-product budgets at the minimum available amount with blocking/pause enabled where GitHub offers it.

## Dashboard Follow-Ups

These providers did not expose a safe CLI/API path for every requested guardrail on this personal/free setup:

- Cloudflare: create an account-wide budget alert in the dashboard for usage-based spend, with Monica and Murad as recipients.
- GitHub: create personal-account budgets/alerts for Actions, Packages, Codespaces, and AI/Copilot-related usage from the billing dashboard.
- Vercel: leave the project on Hobby. If upgraded to Pro, configure Spend Management immediately.
- Convex: verify the team remains on Free. If upgraded, set spending disable to `$0/month`.
