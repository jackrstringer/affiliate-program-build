# New Affiliate Program Build

Portable skill pack for standing up a brand’s **Social Snowball** affiliate emails in **Klaviyo**.

Kickoff (paste this to any agent, or drop this folder into Claude Projects / Cursor):

```
New Affiliate Program Build for [brand].
Read README.md then SKILL.md then SETUP.md then styles/README.md.
Show me the style library as images and ask which family to use.
Do not assume access to any other brand’s Klaviyo.
```

## What this pack is

| File | Role |
|---|---|
| `SKILL.md` | Agent skill. Load this first in Claude/Cursor. |
| `SETUP.md` | The product: kit list, visual laws, cutover, gotchas. |
| `styles/` | Approved families with **phone PNG examples**. Show these. User picks. |
| `CLIENT.template.md` | Empty client file. Copy to `clients/<slug>/klaviyo.md` and harvest. |
| `BOOT.md` | Access the agent needs (Klaviyo key, nothing else assumed). |

No API keys live here. No other brand’s Klaviyo is required.

## Style library (user picks one)

Show the PNGs. Do not describe them instead of showing them.

| ID | Folder | When to use |
|---|---|---|
| **F** (default) | `styles/cased-f/` | Dark editorial. Greeting + headline + sub. Accent on the link chip. |
| **D** | `styles/cased-d/` | Dark quiet receipt. Accent on the earn figure + filled CTA. |
| **E** | `styles/cased-e/` | Paper / serif. Almost no accent. |
| **eskiin-d** | `styles/eskiin-d/` | Light canvas, lime pills, split stats. Only if they pick it. |

Then **restyle** wordmark, accent, type to *this* brand. If the output still looks like the example brand, the setup failed.

## You still need from the human

See `BOOT.md`. Short version: Klaviyo private key, offer terms, light + dark transparent wordmarks, optional hero/lifestyle crops, accent hex, support URL.
