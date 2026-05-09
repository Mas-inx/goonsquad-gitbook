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
| `designId` | number | The published design's `id` from the designs table. |

Returns `true` if the item was added, `false` otherwise (player offline, missing item definition, design not published, etc).

### `deleteDesign(designId, actorIdentifier?, actorName?)`

Hard-delete a published design. Frees the slot, unequips it from every online player wearing it, drops every persisted wearable record referencing it, and writes an audit entry.

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
| `gscd_delete_design [designId]` | yes | `add_ace group.admin command.gscd_delete_design allow` |
| `gscd_rescan` *(advanced)* | yes | `add_ace group.admin command.gscd_rescan allow` |
| `gscd_rebuild_pool` *(advanced)* | yes | `add_ace group.admin command.gscd_rebuild_pool allow` |

> The rescan and rebuild commands are for hot-reload scenarios. For everyday use, **restarting the server is the simpler and recommended way** to apply new garment templates or trigger auto-expansion — the boot routine handles both automatically.

See [Usage](usage.md#admin-commands) for what each command does.

---

## Server Events

### Clearing a player's custom textures

To wipe a single component's custom texture from a player:

```lua
TriggerEvent('gs-clothdesigner:server:clearWearableState', { componentId = 11 })
```

To wipe **all** custom textures from a player (including the saved record so they don't come back on rejoin):

```lua
TriggerEvent('gs-clothdesigner:server:clearWearableState', {})
```

Useful for "Clear outfit" buttons, character editors, or job uniform systems.

---

## Database Tables

For external integrations (e.g. a website or admin panel that lists each player's designs), the most useful tables are:

| Table | Useful columns |
|-------|----------------|
| `gs_clothdesigner_designs` | `id`, `design_uuid`, `owner_identifier`, `owner_name`, `name`, `status`, `preview_data`, `component_id`, `version`, `created_at`, `updated_at` |
| `gs_clothdesigner_active_wearables` | `owner_identifier`, `component_id`, `design_id`, `model_hash` — what each player currently has equipped |
| `gs_clothdesigner_audit` | `owner_identifier`, `action`, `target_type`, `target_id`, `payload_json`, `created_at` |

`status` on the designs table is `'draft'`, `'published'`, or `'archived'`. Only `'published'` rows can be printed and worn.

`preview_data` is either an `http(s)://` URL (when Discord upload is configured) or a `data:image/png;base64,...` URL. Either way, dropping it into an `<img src="...">` tag will render the design preview directly.
