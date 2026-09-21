# Listing Validator Rules

Versioned marketplace rules for the **Listing Readiness Validator** Base addon. Keeping them here (outside the addon code) lets us update rules without republishing the addon.

## Files

- `rulepack.v1.json` — the live rulepack. The addon fetches it daily via cron, or on demand via the "Refresh rules now" button in settings, and caches it in Memory.
- `schema.v1.json` — the structure contract for `rulepack.v1.json`.

## Stable URLs

- rulepack: `https://raw.githubusercontent.com/mmanovbl/listing_validator_rules/main/rulepack.v1.json`
- schema: `https://raw.githubusercontent.com/mmanovbl/listing_validator_rules/main/schema.v1.json`

The rulepack URL above is the addon's built-in default. Individual accounts may override it with an alternative HTTPS URL in the addon settings.

## How the addon validates a pack

Before activating a fetched pack the addon rejects it (and keeps the last valid cached version) when:

- it is not fetched over HTTPS, exceeds the size limit, or is not valid JSON;
- `schema_version` is unknown or `engine_version` is newer than the addon's engine;
- a profile uses a `validator` outside the closed engine set;
- count limits are exceeded, or a regex is too long / not safely parseable.

The JSON is treated as **data only** — nothing in the pack is executed.

## Closed validator set (engine operators)

| validator | marketplace family | notes |
|---|---|---|
| `allegro_v2` | Allegro | title min chars/words + max, HTML tag allow-list, forbidden off-platform sale/promotion, external sales links, byte limit, image source |
| `allegro_v1` | Allegro (legacy) | same engine as `allegro_v2` |
| `drmax_cz_v1` | Dr.Max CZ | title length/brand/quotes/caps, structured description checks, dimensions in mm/g |

Adding a marketplace that reuses one of these validators with different thresholds, regexes, domains, tags or dimensions is a **rulepack-only** change. A marketplace that needs new logic requires a new validator in the addon engine (an addon release).

## Editing workflow

1. Edit `rulepack.v1.json`. Keep `schema_version` and `engine_version` unchanged unless the addon engine changed.
2. Bump `pack_version` and `published_at`.
3. Keep JSON strict (no comments, no trailing commas).
4. Commit and push to `main`. Live accounts pick up the change on the next daily cron, or immediately via "Refresh rules now".
