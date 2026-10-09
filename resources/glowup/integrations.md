# Integrations

The bridge files remain editable for server-specific integration. GlowUp uses the configured adapters for identity, economy, inventory, interactions and notifications; no job is required.

## Framework and Inventory Detection

Automatic framework priority is Qbox, QBCore, ESX, then standalone. Inventory priority is ox_inventory, qb-inventory, qs-inventory, lj-inventory, then ESX inventory. If none is detected, ESX falls back to its native inventory, QB/Qbox selects the QB adapter, and standalone selects the built-in bag.

| Config value | Adapter |
|---|---|
| `Framework = 'qbox'` | `qbx_core` player and usable APIs |
| `Framework = 'qb'` | `qb-core` core object and player functions |
| `Framework = 'esx'` | `es_extended` shared object and player functions |
| `Framework = 'standalone'` | License-based identity and internal wallet |
| `Inventory = 'ox'` | ox_inventory count, capacity, item and lifecycle APIs |
| `Inventory = 'qb'` | qb-inventory exports with framework item-function fallback |
| `Inventory = 'qs'` | qs-inventory exports with framework item-function fallback |
| `Inventory = 'lj'` | QB-compatible framework item functions |
| `Inventory = 'esx'` | ESX native inventory functions |
| `Inventory = 'none'` | Saved internal item counts and the app bag |

For a QB/Qbox deployment, install its compatible inventory before GlowUp rather than relying on an absent inventory to supply APIs. The ESX native-inventory adapter expects an ESX player object. There is no dedicated `ps-inventory` adapter.

Qbox/QBCore identify characters by citizen ID; ESX uses its player identifier. Standalone tries license, license2, Discord, Steam, FiveM and IP identifiers in that order. If a standalone server has multiple characters per license, adapt `Bridge.GetIdentifier` before deployment so profiles are character-specific.

`Config.MoneyAccount` selects cash/bank for rewards and sales. ESX cash maps to `money`. Shop checkout explicitly offers supported accounts. The standalone wallet and built-in bag persist in profile `extra` state; they do not share another resource's economy.

## Usable Items

Equipment registers through the selected framework. Cosmetic registration depends on `Config.ProductUse.enabled`. See [Installation](installation.md#usable-items) for standalone external-inventory export wiring.

The bridge records use handlers in `Bridge.Usables`. The internal bag dispatches only registered handlers for an item the player owns. External inventory implementations must supply compatible count/add/remove and usable-item APIs; adjust the open server bridge where your installation differs.

Crafted products include `crafted_by` and `quality = 'Studio'` metadata where the inventory supports it. ESX's native inventory and the built-in bag store counts without that metadata; crafting and sales still work.

Capacity checks are strongest in the ox adapter. Other adapters use their available capacity API and rely on item-addition results. Make sure custom adapters return accurate success/failure values and preserve refund behaviour.

## Interaction, Notifications and Progress

Target detection prefers ox_target, then qb-target, then the built-in E prompt. The bridge binds local supplier/equipment entities and the buyer/thief interaction. Helpers should start before GlowUp.

When enabled and started, ox_lib is loaded through `shared/bridge.lua`. Notifications then prefer it, followed by framework notifications and the interface fallback. Progress prefers the helper unless `Config.Progress = 'nui'`. Set both `Notify` and `Progress` to `'nui'` for GlowUp's themed HUD, or `UseOxLib = false` to skip helper loading.

## Persistence and Updates

`server/storage.lua` contains the JSON/oxmysql adapter. GlowUp creates its SQL tables automatically, and saves dirty profiles periodically, on disconnect and on orderly shutdown. JSON state is written to `storage/data.json`; placed equipment is persisted separately within that same store or its SQL table.

Back up the active backend before updates. Keep `storage/data.json` when replacing a JSON installation. Changing backends does not copy existing state; migrate explicitly through the open adapter. In-progress sales, crafts, applications and stolen records are runtime state rather than durable sessions.

## Legacy Studio Import

Fresh installations ship with `Config.Import.enabled = false`. For an existing studio migration, back up both stores and set:

```lua
Config.Import = {
    enabled = true,
    resource = 'gs-plastics',
    tablePrefix = 'gs_plastics',
}
```

The source is read without changes. JSON mode reads `<resource>/storage/data.json`; SQL mode reads `<prefix>_players` and `<prefix>_placements` in the current database. Both source and destination must use the corresponding backend for this importer.

Players import only when they have no GlowUp profile and the source contains studio activity. The importer copies XP, studio craft/sale/rejection/theft/recovery stats, relevant studio awards and their claim states, and maps the old final-rank award to `master_stylist`.

It does **not** copy earnings, tips, goals, standalone wallet/bag or owed items. Existing GlowUp profiles are left intact. Equipment placements import once and set the `import_placements` marker. Enable import only once the source data is reachable; starting without it can still set the placements marker. Do not clear the marker and re-import into an existing destination without reconciling duplicate equipment.

Disable import again after checking the migrated data. An imported mixer must align with a table's current attachment position to be usable; old layouts may need repositioning.

## Appearance

Optional application changes native head overlays in `client/studio.lua`, using the product's `look` data. It operates on female/male freemode models and does not call an appearance resource's save API. Reloading a saved look replaces the applied changes.

For persistent cosmetics, provide an appearance integration in your server's own appearance workflow. The core application file is escrowed; the shipped product data controls which native overlays are applied but does not implement persistence. Set `ProductUse.applyLook = false` for animation-only use.

## Locales and Interface Assets

Five language JSON files ship in `locales/`. A player's `/glowlang` choice is stored in a local resource KVP. Missing keys fall back to English. Keep interpolation placeholders such as `{count}`, `{item}` and `{xp}` consistent when translating.

`web/img/` contains character cut-outs, scene backgrounds, cosmetic renders, rank emblems, awards and interface icons. Item art falls back from WebP to `inventory/<item>.png`, then a monogram. Custom item names need matching filenames; changing product categories or adding equipment shortcuts also needs an interface adjustment.

## Webhooks

Webhook URLs belong in `server/config.lua`. `sales` logs sales, thefts and recoveries; `awards` logs unlocks and rank-ups; `admin` logs specific rejected timing/distance requests. Logging does not create administrator commands or grant gameplay rewards. Empty channels are disabled.
