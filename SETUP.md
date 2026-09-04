# Social Snowball one-time setup

This file is the product. Pair with README.md + BOOT.md + styles/.

**Do not assume Klaviyo access to any previously built brand.** Harvest the account in *this* session from the key in BOOT.md.


Last locked: **2026-09-04** (Kleo + Jack, CASED kit). Redo from this file, not from chat.

Master skill: `SKILL.md` (Klaviyo data shape). This file is **the product**: what to build, how it should look, what to ask for, what we already approved.

---

## What we are building

A **reliable one-time setup** for a brand's Social Snowball program in Klaviyo. Not a one-off campaign. Not the 5-email stock nurture Social Snowball clones in.

Show Jack phone PNGs. He picks. Clone onto live triggers. Old stock → draft. Manual-add series left alone unless he asks.

**Copy is GPT.** Parent does not write SS copy. Haiku only renders. See `feedback-ss-copy-no-invented-objections` and `feedback-haiku-for-render-grunt`.

---

## Average build: the kit

### Onboarding (4 looks, 2 jobs)

| # | Job | Trigger (name) | Copy job |
|---|---|---|---|
| 1 | Purchase enroll, default | Enrolled in SocialSnowball through purchase | Surprise. They did not opt in. Headline must say **refer and earn**. Sub states both sides of the offer. Link chip. |
| 2 | Purchase enroll, photo | same | Same copy. Ride/product photo under the logo. |
| 3 | Signup form, default | Signed up as an affiliate via sign up form | They meant it. Welcome / it's official. Link chip. CTA often **Add payout method**. |
| 4 | Signup form, photo | same | Same copy, photo hero. |

Do not reuse purchase copy on the form email.

### Transactionals (7)

Default look: **family F**. Restyle color/type to the brand.

| # | Job | Typical metric | Notes |
|---|---|---|---|
| 5 | Affiliate changed program | Affiliate changed program | State the new offer. Skip only if Jack says the program is flat and this flow is unused. |
| 6 | Approved application | Approved affiliate application | Link chip. CTA Add payout method. |
| 7 | Discount code / link changed | Discount code changed | Show the **new link**, not the code, unless Jack asks for the code. Old bind bug: live stock often labels "Discount code" and binds Referral Link. |
| 8 | New referral sale | New referral sale | Money that just moved. Order + earned. Bind profile `Affiliate Order Value` + `Payout amount`. Values often already include `$`. |
| 9 | Payout sent | Payout sent | Paid amount. No link chip unless useful. |
| 10 | Pending application | Pending affiliate application | No selling. No link chip. |
| 11 | Rejected application | Rejected affiliate application | No selling. No link chip. CTA Contact us. |

**Out of scope unless asked:** 5-email stock onboarding clones, SMS, IG post/reel/story, welcome-gift, Shopify import / manual upload signup, announcement campaign.

**Leave alone unless asked:** live manual-add series (eskiin `RbrwB2`).

**Never** `transactional: true` unless Jack explicitly asks.

---

## What you need from Jack / the account (before build)

### Always

1. **Writable Klaviyo key** in `.env` (`<SLUG>_KLAVIYO_PRIVATE_API_KEY`). Composio has no Klaviyo OAuth.
2. **Offer terms** as they sit in Social Snowball today (friend % / affiliate $). Use as `|default` only. Live value always wins. Confirm dashboard vs a test profile (CASED test profile was stale 0% / $10 vs real 15% / $20).
3. **Wordmark on light** and **wordmark on dark** (PNG, not a screenshot of the site).
4. **Accent color** (one). If missing, harvest from live buttons.
5. **Support URL** and whether dashboard CTA is signup or login (`https://affiliates.socialsnowball.io/auth/affiliate/signup` vs `/login`).
6. **A real affiliate profile** (name, link, code, order value, payout amount) for `render_with_context`. Never freeze samples into the template.

### For the photo onboarding upgrade

7. **One hero photo** the brand already uses (ride / product / lifestyle). Do not invent. CASED used Klaviyo asset `355852832` (moto). eskiin welcome A used STEF6713.1 `ad49daf8-…jpeg`.

### Harvest (you do this)

```
GET /api/metrics  →  name contains Social Snowball / SocialSnowball
GET /api/metrics/{id}/flow-triggers  →  which flow is actually live
account_custom_fonts()  →  exact family strings. Missing cut = stop, they upload it.
```

Events are almost empty. Bind `person|lookup:'…'`. Re-verify whether Order Value / Payout amount already include `$` on **this** account (CASED and eskiin both did; concatenating currency → `$$`).

Client facts go in `blocklab/clients/<slug>/klaviyo.md`. Copy `klaviyo-dnd-template/CLIENT.template.md` if missing.

---

## Visual laws (do not regress)

1. **No tracked eyebrows.** No left-aligned `PURCHASE ALERT` with wide letter-spacing. Sentence-case headlines.
2. **Thin weights** on F (300–400). Accent used once (chip, or earn figure, or CTA, not all three).
3. **Closer, not a fake universal footer.** Native DnD: he can drop the saved footer in Klaviyo, so skip a designed one. **CODE/HTML cannot take that footer.** Every HTML SS email must end with a quiet closer (muted, one line) **and** `{% unsubscribe %}` so Klaviyo does not append its own. Check this on every build. No social-icon / mountain / legal novel.
4. **Presets restyle.** Take the armature. Discard eskiin lime/Poppins and CASED gold/Helvetica when the next brand is neither.
5. **Phone only** unless he asks for desktop.
6. **Welcome / enroll must name the program.** Headline contains refer (or share) AND earn. Sub states both sides of the offer. Do not be cool or vague. Solawave / Divi / Duradry are the clarity bar, not the voice.

Copy laws: no invented objections ("no tiers"), no em dashes, talk to the person, GPT writes it. Full text in `SKILL.md` and `feedback-ss-copy-no-invented-objections`.

---

## Approved style library

Index: `styles/README.md`. PNG refs live next to it.

| ID | Status | Use |
|---|---|---|
| **eskiin-d** | Live on eskiin (2026-08-27/29) | Lime/white receipt. Welcome A (photo + Give/Get) on signup. Sale/payout/link are D. |
| **cased-d** | Approved family, not the CASED kit | Dark quiet. Zapier-like receipt. Gold on earn + filled CTA. |
| **cased-e** | Approved family, not the CASED kit | Paper `#f6f4ef`, Georgia display, almost no gold. |
| **cased-f** | **CASED kit default** (2026-09-04) | Dark editorial. Greeting + headline + specific sub. Gold link chip. Dashboard is a text link. |

When standing up a new brand: restyle **F** in their colors first (transactionals + onboarding split). Offer D/E if he wants a second pass. Do not start from eskiin-D unless he asks.

HTML generators:

- CASED F kit: `blocklab/clients/cased/campaigns/ss-transactional-mock/generate_f_kit.py`
- CASED D/E/F sale mocks: same folder `generate.py` + `d.html` `e.html` `f.html`
- eskiin D live: `klaviyo-dnd-template/examples/eskiin-ss-d-live.py`

Figma boards (CASED):

- Families D/E/F: `blocklab/clients/cased/campaigns/ss-transactional-mock/figma/board-DEF.png`
- Full F kit: `…/figma/kit-f/board.png` + `01`–`11` `*-3x.png`

---

## Decisions (2026-09-03 → 09-04)

| Decision | Why |
|---|---|
| F is the boring-transactional default | Jack picked D, E, and F as families, then F for the full kit. |
| Onboarding is 2 jobs × default/photo | Purchase = surprise enroll. Form = they signed up. Photo is an upgrade of the same copy. |
| Full 11-email kit, not 4 | Average SS account has the 7 metrics. Pending/rejected get no link chip. |
| GPT writes copy | Parent copy felt like Zuckerberg. No em dashes. |
| Purchase headline must say refer + earn | "You can earn cash from the gear you already ride in" did not tell them what the email was. Comps: Solawave "Refer and earn", Divi "Give $10 Get $10", Duradry "Share your code, Earn $10". Locked: **Refer riders. Earn $20.** |
| No "no tiers" | Invents an objection. |
| No tracked eyebrows | Screams Claude. |
| Haiku for render grunt | Parent burned tokens on screenshot scale. |
| Link in the gold chip, not in the sub | URL-in-sub + chip is redundant. |
| HTML closer + `{% unsubscribe %}` | CODE cannot take a Klaviyo universal footer. Missing unsub = Klaviyo appends its own. Jack 2026-09-04. |
| Jack Figma pass 2026-09-04 | Short chip (`ridecased.com/CODE`). Label "Here's your personal referral link:". CTA "View Referral Dashboard →". Offer numbers slightly emphasized. Photo on purchase/form/program/approved/link/payout. Pending/rejected stay logo-only. |
| eskiin live stays D + Welcome A | Separate brand. Do not restyle eskiin to F unless asked. |

---

## Assets used (so we don't re-hunt)

### CASED

| What | Where |
|---|---|
| Wordmark on dark | `https://d3k81ch9hvuctc.cloudfront.net/company/R6vJL8/images/e011ea1e-0e4f-4547-9231-4a94116cc4f0.png` (asset `355851106`) |
| Wordmark on light | `https://d3k81ch9hvuctc.cloudfront.net/company/R6vJL8/images/a943db02-7070-4ac4-bb8e-e74ba4a49e95.png` (asset `355851104`) |
| Moto hero (photo onboarding) | asset `355852832` — `…/7070c9d2-eba5-4399-af65-e422793f9cae.png` |
| Gold | `#F1AA38` |
| Type | Helvetica / Arial (this program). Other CASED mail uses Roboto Condensed — do not mix. |
| Offer | Give 15% / Get $20 |
| Sample | Rhett / `ridecased.com/RHETT20` / order $189 / earned $20 |
| Key env | `CASED_KLAVIYO_PRIVATE_API_KEY` |
| Copy | `blocklab/clients/cased/campaigns/ss-transactional-mock/COPY.md` |
| Inspo Jack sent | CASED campaign (black / gold outline CTA / wordmark) + Solawave / Divi / Duradry / BREZ / Mink welcomes |

### eskiin (live)

| What | Where |
|---|---|
| Wordmark on light | `clients/eskiin/assets/ss-transactional/logo-on-light.png` → Klaviyo `363105436` / `…/61032641-95c9-420f-93de-7abe360910cf.png` |
| Wordmark on dark | `clients/eskiin/assets/ss-transactional/logo-on-dark.png` |
| Welcome A hero | STEF6713.1 `…/ad49daf8-0272-40b7-b4b5-5b8edfb87924.jpeg` |
| Lime / ink | `#D6FF00` / `#1A1A1A` |
| Type | `Poppins-Klaviyo-Hosted` 400/700. Company `YueHuz`. |
| Offer (live profiles) | Give 25% / Get $50 (not 15/20) |
| Sample | MARIVEL / `eskiin.com/MARIVEL32079` / $275.41 / $50.00 |
| Live flows | Welcome `TN8xdi` (msg 1 live, 2–5 draft) · Sale `SicEVS` · Payout `XwdNLx` · Link `XrAyr9` |
| Old drafted | `SXRq7v` `XvDZ6i` `XXqx29` `X3Ttat` |
| Key env | `ESKIIN_KLAVIYO_PRIVATE_API_KEY` |

---

## How to redo (next brand)

1. Read `SKILL.md` then this file then `styles/README.md` then `clients/<slug>/klaviyo.md` (create if missing).
2. Harvest metrics + fonts + one affiliate profile.
3. Ask GPT for copy (purchase vs form split; F armature; no em dashes; welcome headline = refer + earn).
4. Restyle F in this brand's wordmark / accent / type. Photo onboarding if they gave a hero.
5. Haiku: 1170-wide HTML → phone PNGs → deliver the board unasked.
6. Jack picks. Clone to live triggers as **draft**, then live when he says. Verify `leftover_liquid=[]` + mobile 390. Write new faces/keys back to the client file the same session.

## Cutover process (locked 2026-09-04, CASED)

Do not invent a new path. This is how eskiin D and CASED F both went live.

1. Harvest metrics (`name` contains Social Snowball / SocialSnowball) and `GET /metrics/{id}/flow-triggers` so you know what is already live or draft.
2. Build **CODE** templates (`editor_type: CODE`), not native, for family F. Bind `person|lookup`. `|default` = this brand's real offer. No footer. `transactional: false`. `smart_sending: false`.
3. `render_with_context` on every template. Gate: `leftover_liquid == []` and no `$$`. Use a real-shaped person payload (name, link, offer, reward, order value, payout).
4. `POST /api/flows` with a metric trigger + one send-email action. From-email harvested from the old live message on that account (CASED: `send@ridecased.com` / `Cased`). Message status `live` inside a flow that is created as `draft`.
5. `PATCH /flows/{id}` `{status: live}` on the new flows.
6. `PATCH` every **old** flow returned by `flow-triggers` on those metrics to `draft`. Leave manual-add / SMS / IG / welcome-gift alone.
7. Re-GET each new flow and confirm `status: live`. Write ids into `clients/<slug>/klaviyo.md` the same session.
8. Onboarding: one live email per trigger. Default vs photo is a pick, not two live flows. CASED shipped the **photo** upgrade for purchase + form.
9. **CODE closer + `{% unsubscribe %}`** on every HTML template. Native can skip a designed footer; HTML cannot take a universal footer.
10. **Flow messages clone the template.** Creating a flow (or PATCHing `flow-actions` with `template_id`) copies HTML onto a new template id. Updating the library template does **not** update what Jack sees in the flow editor. After any HTML edit: re-point the live `flow-action` at the patched source, then GET the **assigned** clone and confirm closer + `{% unsubscribe %}` are in *that* HTML. Check the flow editor (hard refresh), not the template library.

Recipe: `blocklab/clients/cased/campaigns/ss-transactional-mock/ship_f_live.py` (CASED) · `klaviyo-dnd-template/examples/eskiin-ss-d-live.py` (eskiin).
