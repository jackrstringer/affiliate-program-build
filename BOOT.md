# Access this agent needs

Do **not** assume Klaviyo for eskiin, CASED, or any other Redwood client exists in this session. Every build is a new account.

## Required

### 1. Klaviyo private API key (this brand)

- Create in Klaviyo: **Settings → API keys → Create Private API Key**
- Scopes (grant all of these; read-only will fail cutover):
  - **Templates** — read, write
  - **Flows** — read, write
  - **Metrics** — read
  - **Profiles** — read (for `render_with_context` + a real affiliate profile)
  - **Events** — read (latest SS event to find a profile)
  - **Accounts** — read (optional; company id / fonts)
- Paste into the agent’s secrets as `BRAND_KLAVIYO_PRIVATE_API_KEY` (example: `ACME_KLAVIYO_PRIVATE_API_KEY`).
- Composio / OAuth **does not** support Klaviyo. This is always a pasted private key.
- The key must be **writable**. A read-only harvest key cannot create flows.

### 2. Brand assets (files)

- Wordmark PNG, **transparent background**, high-res, **two files**:
  - on light (dark/black mark)
  - on dark (white/light mark)
  - Not a screenshot of the website.
- Optional but needed for photo onboarding: 1–3 landscape hero / lifestyle / product-in-use crops (the kind used in the F examples).

### 3. Facts

- Offer as it sits in Social Snowball **today**: friend discount + affiliate payout (e.g. 15% off / $20 cash).
- Site URL, support/contact URL.
- Accent hex if they have one (else harvest from live buttons).

## Optional

- ScreenshotOne (or any HTML→PNG) key if the agent should screenshot renders. Not required to ship to Klaviyo.
- From-name / from-email. If omitted, harvest from an existing live flow on **this** account.

## Explicitly not required

- Access to any other brand’s Klaviyo.
- Klaviyo OAuth / Composio Klaviyo.
- Figma (phone PNGs are enough).
- The brand’s Social Snowball dashboard login (nice to have to confirm offer terms if the test profile looks stale).
