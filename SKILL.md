---
name: Social Snowball Klaviyo Program Builder
description: Run a full New Affiliate Program Build for Social Snowball in Klaviyo — harvest, style-library pick, GPT copy, CODE templates, live RW flows, old stock drafted. Use whenever the user says "New Affiliate Program Build", asks to set up an affiliate/referral program's emails, or mentions Social Snowball. Read SETUP.md first. Builds on klaviyo-dnd-template.
metadata:
  version: "1.2.0"
---

# Social Snowball Klaviyo Program Builder

**One-time setup (the product):** `SETUP.md` — kit list, assets, decisions, how to redo.
**Style library:** `styles/README.md` — eskiin-D (live) + CASED D/E/F.

Social Snowball is a Shopify affiliate/referral app that integrates with Klaviyo by pushing
events + profile properties. This skill is the map of that integration, learned by directly
probing a live account (Cased/ridecased.com) rather than trusting Social Snowball's own docs,
which don't cover the Klaviyo-side data shape at all.

## The one thing to internalize: variables live on the PROFILE, not the event

Social Snowball's Klaviyo events carry almost no payload — just `$event_id`, and on "New
referral sale" a raw `$value` (the referred order's total). Everything else (link, code, offer
%, commission, payout amount) is written as **custom properties on the affiliate's Klaviyo
profile**, overwritten in place every time something changes. So dynamic content in these
emails almost always uses `{{ person|lookup:'Property Name' }}`, not `{{ event.property }}`.

## Exact profile property keys (case-sensitive, verified live)

```
{{ person|lookup:'Affiliate First Name'|default:'there' }}
{{ person|lookup:'Referral Link - SocialSnowball'|default:'' }}
{{ person|lookup:'Referral Code - SocialSnowball'|default:'' }}
{{ person|lookup:'Referral Friend Offer - SocialSnowball'|default:'15%' }}   -- what the referred friend gets, PRE-FORMATTED (e.g. "15%")
{{ person|lookup:'Referral Reward - SocialSnowball'|default:'$20' }}        -- the flat per-sale rate, PRE-FORMATTED (e.g. "$20") -- descriptive, NOT what to show for a specific sale's commission (see below)
{{ person|lookup:'Social Snowball: Affiliate Order Value'|default:'' }}     -- PRE-FORMATTED with currency symbol already in it (e.g. "$44.75") on this account
{{ person|lookup:'Social Snowball: Payout amount'|default:'' }}             -- PRE-FORMATTED with currency symbol already in it; ALSO the correct field for "your commission on this sale" in a Sale Made email (see below)
{{ person|lookup:'Social Snowball - Affiliate Status'|default:'' }}         -- Active / Pending / Rejected
{{ person|lookup:'Gift card code - Social Snowball'|default:'' }}
{{ person|lookup:'Referral Currency - SocialSnowball'|default:'$' }}       -- exists as its own property, but do NOT concatenate it onto Order Value / Payout amount on this account -- see below
```

**Do NOT prepend `Referral Currency - SocialSnowball` onto Order Value or Payout amount —
verified live 2026-08-07 that both already carry the `$` in this account's real profile data,
and concatenating produces `$$44.75`.** Social Snowball's own generic default "Sale Made"
template (seen in their in-app editor, screenshotted by the client) does concatenate a
currency tag onto bare numbers — but that is NOT what this account's actual live Klaviyo
profile data looks like. Trying to "match the vendor's reference template" here was the wrong
call: a generic example screenshot is not as trustworthy as this account's own data, which was
already pulled directly from the API earlier in the same session (`Social Snowball: Affiliate
Order Value` = `"$44.75"` on the real test profile, currency symbol included). **When a generic
vendor example conflicts with what you already pulled live from THIS account, the account's own
data wins — always re-verify against the specific account you're building for, don't assume its
data shape matches a generic screenshot, even one that's clearly the vendor's own UI.** This
flip-flopped once (unprefixed → prefixed → unprefixed again) before landing here; the account's
live data was correct from the very first pull and should have been trusted throughout.

**For a Sale Made email specifically, the per-sale commission field is `Social Snowball: Payout
amount`, not `Referral Reward - SocialSnowball`.** `Referral Reward` describes the affiliate's
standing rate; Social Snowball's own default template pulls `Payout amount` at send time to
show what was actually earned on that specific sale. Use `Referral Reward` only for general
"here's your rate" copy (announcements, enrollment), not a specific-sale confirmation. (This
part of the vendor reference template DID hold up against live testing — it's specifically the
currency-concatenation detail that didn't transfer from their generic example to this account.)

Confirmed the `|default:''` chaining pattern generally by reading the raw HTML of Social
Snowball's own stock templates (both the post-purchase-enrollment one and the sale-notification
one) still live on real flow messages. But "confirmed via a template we can read" and "confirmed
via this account's own live data" are different strengths of evidence — prefer the latter, and
re-pull the latter fresh rather than trusting a memory of it from earlier in a long session.

## Rule: offer numbers are ALWAYS dynamic variables, never hardcoded text

If the brand ever changes their referral program's %/$ terms in the Social Snowball dashboard,
every email should update itself with zero template edits. Concretely:

- ❌ `"GIVE 15%. GET $20."`
- ✅ `"GIVE {{ person|lookup:'Referral Friend Offer - SocialSnowball'|default:'15%' }}. GET {{ person|lookup:'Referral Reward - SocialSnowball'|default:'$20' }}."`

Use the brand's *current real offer* as the `|default` fallback value, so the copy never
renders blank/broken if a property is ever unset — but the live value always wins when present.

Prove this works before calling it done: render the template twice via
`render_with_context` (see `klaviyo-dnd-template/lib.py`) with two different sets of
Friend-Offer/Reward values, screenshot both, and confirm the copy actually changed. Don't just
assert it's dynamic because the tag is present — a typo'd property name renders as an empty
string just as easily as a correct one does.

Static/non-dynamic and safe to hardcode: anything describing program *structure* rather than
the specific numbers — "No tiers", "paid straight to your account", etc. Those stay true even
if the % or $ amount changes.

## Discovering which flow maps to which event, for a given account

Social Snowball ships ~16 metrics per connected brand. List them and find the ones you need:

```
GET /api/metrics   →  filter attributes.name for "Social Snowball" / "SocialSnowball"
```

Typical set: signup variants (manual upload / CSV import / sign-up form / manual creation),
"Enrolled in SocialSnowball through purchase", "Discount code changed", "Welcome Gift Claimed",
"New referral sale", "Payout sent", "Approved/Pending/Rejected affiliate application",
"Affiliate changed program", IG post/reel/story added.

To find which flow (if any) is already wired to a given metric, don't guess from flow names —
ask Klaviyo directly:

```
GET /api/metrics/{metric_id}/flow-triggers   →  returns the flow(s) actually triggered by it
```

This is the only reliable way to match e.g. "Enrolled in SocialSnowball through purchase" to a
flow named something opaque like "6. Email - Template - Social Snowball - Post purchase -
Simplified follow ups (clone)" — the numbered/cloned names Social Snowball ships do not
reliably describe their trigger.

**Don't assume a flow exists for every metric.** In the reference account, 5 of the 6 cloned
"Email - Template - Social Snowball -..." flows had real triggers via `flow-triggers`, but
"Payout sent" had none — no flow was ever built for it, despite Social Snowball listing it as
part of their standard "Base Setup". Verify before building on top of an assumed flow.

## Flow email inventory (the "Base Setup" a brand typically wants)

- **Onboarding / announcement** — campaign send introducing the program, not flow-triggered.
- **Enrolled via purchase → deliver link** — usually a 3-email flow: link delivery immediately
  (short delay, e.g. 10 min), then 1-2 reminder emails at short intervals (observed: +2 days,
  +5 days — much tighter than a typical "post-purchase nurture," don't assume long gaps).
- **Enrolled via signup form** — same shape, separate flow, separate trigger metric.
- **New referral sale → notify affiliate** — single email. **Use Social Snowball's own default
  template as the reference, not the raw triggering event.** Their own "Sale Made" template
  pulls order value and commission from PROFILE properties (`Social Snowball: Affiliate Order
  Value` + `Social Snowball: Payout amount`, both currency-prefixed per the formatting rule
  above), not from `event.$value`. An earlier version of this skill reasoned its way into
  preferring `event.$value` instead, on a theory that the profile property could go stale if a
  second sale lands before the first email sends — a plausible-sounding theory that turned out
  to not match the vendor's own implementation. Social Snowball controls both the event and the
  property write server-side, so trust their own template's field choice over a theoretical
  race condition you haven't actually observed. (Separately, if you ever do need the bare event
  value: `{{ event.$value }}` dot-notation is invalid Django syntax — the `$` breaks the parser,
  confirmed live with a 400 even in an isolated test template. It would need
  `{{ event|lookup:'$value'|floatformat:2 }}` instead.)
- **Payout sent → notify affiliate** — single email, order-value-style race condition doesn't
  apply the same way (payouts are periodic, not simultaneous) so the profile property
  `Social Snowball: Payout amount` is fine to use directly.
- **Program/tier changed** — only relevant if the brand actually has program tiers; skip
  entirely for a flat-rate (no-tier) program.
- **Evergreen highlight blocks** — small reusable "Give X% Get Y" modules for dropping into
  unrelated flows (welcome series, post-purchase, browse abandonment). Build these as plain
  copy blocks using the same dynamic variables, not tied to any Social Snowball trigger.

## Dashboard links (confirmed real, don't guess a Social Snowball URL from memory)

```
Set password / first-time dashboard access: https://affiliates.socialsnowball.io/auth/affiliate/signup
Returning-affiliate login:                  https://affiliates.socialsnowball.io/auth/affiliate/login
```

These are account-agnostic (not brand-specific subdomains) as of this writing — confirm with
the user if a brand's Social Snowball setup looks customized, but don't assume a different
pattern without checking.

## Visual laws (Jack 2026-09-03 — do not regress)

These are **skill laws**, not CASED-only. They exist so a one-time Social Snowball setup for a new brand does not come out looking generated.

1. **No tracked eyebrows.** Never left-align an uppercase kicker with wide letter-spacing (`PURCHASE ALERT`, `REFER AND EARN`, `YOUR LINK`). Sentence-case headline, regular tracking. Section labels if needed: sentence case, weight 400, `letter-spacing: 0`.
2. **Thin weights.** Body and headlines 300–400. Do not default to 600/700 everywhere. Gold/accent is used **once** per email (the earn figure, or the link, or the CTA — not all three).
3. **Quiet armature.** Headline → one or two receipt lines → keep-sharing line + link → one CTA. No split-stat cards with colored underlines unless the brand already has that. No dark “refer and earn” marketing band on a transactional.
4. **Closer + unsubscribe on every CODE/HTML email.** Native DnD can skip a designed footer (he drops the saved one). CODE templates **cannot** take a Klaviyo universal footer, so every SS HTML email ships a quiet ending: one muted line + `{% unsubscribe %}`. Check this on every build. If `{% unsubscribe %}` is missing, Klaviyo appends its own ugly unsub block. Do not invent social/legal bands.
5. **Presets restyle.** Take the armature. Discard source identity (eskiin lime / Poppins, CASED gold / Helvetica). If the output still looks like the last brand, the setup failed.

## Approved families (CASED board, 2026-09-03)

Jack liked **D, E, and F**. A/B/C (eskiin-D restyle) are discarded. Use these as the starting board when standing up a new SS account; restyle color/type to the brand.

| Family | Canvas | Type | Accent use |
|---|---|---|---|
| **D** Dark quiet | `#0a0a0a` | Helvetica 400 | Gold on the earn figure + one filled CTA |
| **E** Paper | `#f6f4ef` | Georgia display / Helvetica body | Almost no accent; black CTA |
| **F** Dark editorial | `#111` | Helvetica 300 | Gold only on the link chip; dashboard is a text link |

Mocks: `/workspace/blocklab/clients/cased/campaigns/ss-transactional-mock/` and `figma/`.

**Default family for the boring transactionals: F.** Onboarding has a default + a photo upgrade. Purchase-enroll ≠ signup-form — different job, different copy.

## Copy laws (Jack 2026-09-04)

- Headline = what happened. Sub = one specific sentence to this person. Then the number / the link / one CTA.
- **Do not invent objections.** No "no tiers", "same rate every time", "no catch" unless this email is actually a program/tier change. The rider was not thinking about tiers until you said it.
- Benefits, if you restate them: they get X% off, you get $Y. Hook your friends up. Get paid. Stop.
- Style F keeps **greeting + headline + specific sub**. Do not drop the sub (the D/E versions had it; F lost it and felt generic).
- Purchase onboarding = surprise ("you just got a link"). Form onboarding = they meant it ("you're in / it's official"). Do not reuse one welcome for both.

## Full kit (one-time SS setup)

**Onboarding (4 looks, 2 jobs)**

| Job | Trigger | Copy job |
|---|---|---|
| Purchase enroll, default | Enrolled through purchase | Surprise. Did you know. Here's the link and how it works. |
| Purchase enroll, photo | same | Same copy, photo hero |
| Signup form, default | Signed up via sign up form | They opted in. Welcome / it's official. |
| Signup form, photo | same | Same copy, photo hero |

**Transactionals (F unless Jack picks otherwise)**

| Job | Typical metric |
|---|---|
| Affiliate changed program | Affiliate changed program |
| Approved application | Approved affiliate application |
| Discount code / link changed | Discount code changed |
| New referral sale | New referral sale |
| Payout sent | Payout sent |
| Pending application | Pending affiliate application |
| Rejected application | Rejected affiliate application |

Do not rebuild the 5-email stock nurture unless asked. Manual-add series left alone unless asked.

## One-time setup (the product)

For a new Social Snowball account:

1. Harvest metrics + live flows + a real affiliate profile (`klaviyo-dnd-template` harvest). Bind `person|lookup`, never freeze samples.
2. Show Jack the kit in **this** brand, family F for transactionals, purchase vs form onboarding split, phone PNGs. No tracked-eyebrow comps.
3. He picks. Clone onto the live triggers. Old stock → draft.
4. Verify `render_with_context` + leftover_liquid=[] + mobile 390 PNGs. Deliver unasked.

## Footer pattern (if the brand wants one baked into a Social Snowball email)

Observed in a real hand-built footer on the reference account: several promo/nav image blocks
(collection links), then a 4-column `table` block of social icons (each `table_image` subblock
with its own `href`), then one small `text` block (~8px gray) carrying the brand blurb, any
guarantee copy, the mailing address, and `{% unsubscribe %}` / `{{ organization.full_address }}`
tags. If the user says "I'll drag the footer in myself," leave it out entirely rather than
building an approximation — footers tend to be an established, reused asset per brand.

## Related

Requires `klaviyo-dnd-template` for the actual block-building mechanics (button alignment,
image sizing, the universal-sections update lock, render+screenshot verification). This skill
is the domain layer on top of that one.
