# {Brand} — Klaviyo client knowledge

Copy this file to `blocklab/clients/<slug>/klaviyo.md` the first time we build native / Flow Message for a brand. Fill every section from a live harvest the same session. Do not invent.

Master skill (how Klaviyo works): `/workspace/.claude/skills/klaviyo-dnd-template/SKILL.md`

## Account

- Slug:
- Key env:
- Company id (from `custom_fonts.css` URL):
- Writable? (Agent Nateur = read-only)
- Site / shop:

## Identities

Name each register. Never mix them on one template.

### {identity name}

- When to use:
- Face + weights (exact `@font-face` family strings from `account_custom_fonts`):
- Display tracking (Figma % → `tracking_em`):
- Horizontal pad:
- Buttons:
- Rules / accent:
- Wordmark assets (light / dark) + Klaviyo `asset_id`s:

## Fonts (this account)

Paste the relevant `@font-face` rows from `account_custom_fonts()`. Map Figma names → family + weight.

| Figma name | Family string | weight | file |
|---|---|---|---|

If a named cut is missing, stop. They upload it in Brand Library. Do not fake Arial.

## Metrics we actually bind

IDs are **this account**. Re-`list_metrics` if anything 404s.

| Job | Platform | Metric name | ID | Packet shape | Notes |
|---|---|---|---|---|---|

Per metric, list keys that exist / do not exist after a live `latest_event`. Update the same session the packet changes.

## Live templates

| What | ID | Recipe | Identity |
|---|---|---|---|

Junk / wrong-metric IDs: list them so nobody ships them.

## Footer

We do not ship one. Where theirs lives (universal content id / saved block / "Jack drops it on"):

## Do not

- 
