# API & Exports

ClothDesigner exposes a small surface for integration with menus, custom job flows, and admin tooling.

---

## Client Exports

### `openDesigner()`

Open the studio for the local player, bypassing the station / job check. Useful for hooking the studio into a custom job menu, key-mapped action, or a different physical location.

```lua
exports['gs-clothdesigner']:openDesigner()
```

The studio still validates the player's framework job + grade against `Config.Designer.jobs` server-side. To make access fully public, add an `unemployed = 0` (or similar) entry to `Config.Designer.jobs`.

---

## Server Exports

### `printShirtItem(source, designId)`

Print a published design as an inventory item to a specific player. Use this for shop integrations, quest rewards, admin tools, or "give me a copy" flows.

```lua
local success = exports['gs-clothdesigner']:printShirtItem(source, 42)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `source`  | number | Server ID of the receiving player. |
| `designId` | number | The published design's `id` from `gs_clothdesigner_designs`. |

Returns `true` if the item was added, `false` otherwise (player offline, missing item definition, design not published, etc).

> Internally calls `GSCD.Designs.PrintPublishedDesignToPlayer`, which validates the design is published and routes through the active inventory backend (ox_inventory / qb-core / ESX).

### `deleteDesign(designId, actorIdentifier?, actorName?)`

Hard-delete a published design. Frees the texture slot, unequips it from every online player wearing it, drops every persisted wearable row referencing it, and writes an audit entry.

```lua
local ok, result = exports['gs-clothdesigner']:deleteDesign(42, 'admin:steam:1234', 'AdminUser')
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `designId` | number | The design's `id`. |
| `actorIdentifier` | string \| nil | Identifier of who triggered the delete. Stored in the audit row. Defaults to `'export'`. |
| `actorName` | string \| nil | Display name of the actor. Stored in the audit row. Defaults to `'Export'`. |

Returns `(true, { id = ... })` on success, or `(false, reason)` on failure (`'design_not_found'`, etc).

---

## Server Commands

| Command | RCON / Console | In-Game (with ACE) |
|---------|----------------|--------------------|
| `gscd_rescan` | yes | `add_ace group.admin command.gscd_rescan allow` |
| `gscd_rebuild_pool` | yes | `add_ace group.admin command.gscd_rebuild_pool allow` |
| `gscd_fill_slots [source_key]` | yes | `add_ace group.admin command.gscd_fill_slots allow` |
| `gscd_delete_design [designId]` | yes | `add_ace group.admin command.gscd_delete_design allow` |

See [Usage](usage.md#admin-commands) for what each command does.

---

## Server Events

### `gs-clothdesigner:server:clearWearableState`

Net event the client fires when it detects a wearable component should be released (e.g. another resource set a different drawable on that slot). You can also fire it from your own resource to forcibly clear a player's custom textures.

```lua
TriggerClientEvent('gs-clothdesigner:client:clearWearable', src, { componentId = 11 })
```

To wipe **all** GSCD wearables from a player (including persisted DB rows so they don't come back on rejoin):

```lua
TriggerEvent('gs-clothdesigner:server:clearWearableState', { source = src })
```

> Omit `componentId` from the payload to fully unequip.

### `gscd:silhouettes:rescanned`

Emitted internally after `gscd_rescan` (or the boot sync) finishes. The summary payload looks like:

```lua
{
    scannedRoots = { 'cloth_templates' },
    totalFound   = 64,
    warnings     = {},
    db = {
        added = 0, updated = 0, unchanged = 64,
        stale = 0, total = 64,
    },
    elapsedMs = 312,
}
```

```lua
AddEventHandler('gscd:silhouettes:rescanned', function(summary)
    if summary.totalFound > 0 then
        print(('[my-resource] %d cloth templates ready'):format(summary.totalFound))
    end
end)
```

---

## Client Events

### `gs-clothdesigner:client:clearWearable`

Sent by the server when a published design is deleted, expired, or the server explicitly tells this client to drop a wearable.

```lua
RegisterNetEvent('gs-clothdesigner:client:clearWearable', function(payload)
    -- payload = { componentId = number? }
    -- Omit componentId to clear all GSCD wearables on this client.
end)
```

You don't need to handle this event yourself — the bundled `client/clothing.lua` already does. Listed here for reference only.

### `gs-clothdesigner:client:silhouetteStatusInvalidated`

Server pings every connected client when a publish or delete may have changed lock state for some silhouette. The studio NUI listens for this internally to refresh its lock overlay map without polling.

---

## Internal JS Exports (Advanced)

The Node-side bundle in `dist/server/silhouettes.js` registers several `gscd*` exports that the Lua side calls. These are documented for completeness — most servers don't need to touch them.

| Export | Description |
|--------|-------------|
| `gscdSyncDiscoveredSilhouettes()` | Re-scans `cloth_templates/` and upserts the `gs_clothdesigner_silhouettes` table. Returns a summary. |
| `gscdGetDiscoveredSilhouettes()` | Returns the in-memory list of discovered silhouettes (last sync). |
| `gscdGetLastDiscoverySummary()` | Returns the last sync's summary object. |
| `gscdAllocateSlotForSilhouette(row)` | Internal: allocate a texture slot for a publish. |
| `gscdReleaseSlot(dlcSuffix, drawableIndex, slotIndex)` | Internal: free a slot when a design is deleted. |
| `gscdGetSilhouetteStatus()` | Returns `{ sourceKey -> { totalSlots, freeSlots, locked } }` for the UI lock overlay. |
| `gscdReattachAndRebuild()` | Boot/rebuild entrypoint for the pool. Same thing `gscd_rebuild_pool` calls. |
| `gscdGetPoolDir()` | Returns the absolute path of the pool's `generated/` directory. |
| `gscdGenerateAiDesign(options)` | Server-side Gemini call for AI Mode. |
| `gscdUploadMediaToDiscord(options)` | One-shot Discord webhook upload. |
| `gscdStartDiscordMediaUpload(options)`, `gscdAddDiscordMediaUploadPart(options)`, `gscdFinishDiscordMediaUpload(options)` | Chunked upload flow for media too large to fit in a single export call. |

> These exports are JS, not Lua. Call them through the FiveM `exports['gs-clothdesigner']:gscdXxx(...)` bridge — same syntax as a Lua export.

---

## Database Tables

For external integrations (e.g. a website or admin panel that lists designs), the most useful tables are:

| Table | Useful columns |
|-------|----------------|
| `gs_clothdesigner_designs` | `id`, `design_uuid`, `owner_identifier`, `owner_name`, `name`, `status`, `preview_data`, `preview_path`, `component_id`, `version`, `created_at`, `updated_at` |
| `gs_clothdesigner_active_wearables` | `owner_identifier`, `component_id`, `design_id`, `model_hash` — what each player currently has equipped |
| `gs_clothdesigner_silhouettes` | `id`, `source_key`, `display_name`, `gender`, `category`, `component_id` — the catalog of available silhouettes |
| `gs_clothdesigner_audit` | `owner_identifier`, `action`, `target_type`, `target_id`, `payload_json`, `created_at` |

`status` on the designs table is `'draft'`, `'published'`, or `'archived'`. Only `'published'` rows can be printed and worn.

`preview_data` is either an `http(s)://` URL (when Discord upload is configured) or a `data:image/png;base64,...` URL (fallback). Either way, dropping it into an `<img src="...">` tag will render the design preview.
