# Troubleshooting

Check the server console and client F8 console. Temporarily enable `Config.Debug = true` for additional logs, then restart GlowUp. Successful startup prints its selected integrations, storage backend and a line containing `GlowUp studio ready`.

## Installation and Access

### Commands do nothing

Confirm `ensure gs-glowup`, its exact folder name, startup errors and framework/inventory/helper start order. The default commands are prefixed with `glow`; `/stats` and `/top` are not GlowUp commands. Check that their `Config.Commands` entries are enabled and do not conflict with another script.

### Actions say female only

The active ped model must be in `Config.FemaleModels` while `FemaleOnly` is enabled. The default is `mp_f_freemode_01`. Add your custom model name or disable the gate. Framework gender/job fields do not replace this check. Enable OneSync for server-side model and distance validation.

### Equipment or cosmetics cannot be used

Register items with the correct names, usable flags and start order. Standalone ox_inventory needs the matching `server.export` entries. Other frameworks register callbacks through the bridge. Cosmetics are intentionally unusable while `ProductUse.enabled = false`. `/glowuse` works only with the built-in bag.

Restart the whole server after editing framework item tables. If the console reports an unfinished string near a backtick in an items file, remove any Markdown fences pasted with the definitions.

## Shop and Equipment

### Supplier or E prompt is missing

Check the configured map location and ped model. When a target adapter is selected, use its interaction instead of expecting an E prompt. Force `Config.Target = 'none'` and restart to use built-in prompts. Close menus during interaction checks. Extra shops require matching target/blip locale keys.

### Buying equipment fails

The workbench costs $12,000 and mixer $8,500 by default. New standalone wallets begin with $25,000; existing wallets retain their saved balance. Check cash/bank selection, registered item names and capacity. Gram-based definitions give equipment weights of 15 kg and 8 kg, each with its own slot.

### The table or mixer is invisible

Keep all six streamed `.ydr`, `.ytyp` and `.ycd` files and both archetype declarations in the manifest. Check for model-name collisions and asset escrow access errors. Restart after replacing assets. GlowUp's custom unfold/fold and dispensing animations need an in-game check on your server.

### The mixer says a workbench is required

Place your own table first, then use the mixer near it. The machine must snap to its table footprint with the matching heading; a mixer merely sitting nearby is insufficient. Legacy equipment layouts may need packing or repositioning. Using a spare equipment item moves an existing deployed unit; packing retrieves it when no spare is available.

### Another player cannot craft or pack

Shared crafting requires `Config.SharedEquipment = true`. Packing is owner-only. A machine reserved by an active craft cannot be moved or packed; complete/cancel that craft first.

## Crafting and Application

### Craft button is disabled

Compare required ingredient counts against the selected batch size. Check recipe/product IDs, inventory registrations and the selected adapter. Batches range from one to five and multiply all ingredient requirements.

### Craft seems stuck on START

Click the glowing machine button or press E. Right-click, Backspace/Esc or X cancels before dispensing. Check the machine offsets and camera configuration if the ring is misplaced. While dispensing, X cancels and returns ingredients.

### Refunded items have not returned

Free weight and slots. Inventory refunds that cannot fit are recorded as owed items and retried every 20 seconds. Preserve the profile backend when updating so those records are retained.

### Makeup does not show or disappears

Enable `ProductUse.enabled` and `ProductUse.applyLook`. Native looks apply only to freemode models and products with a `look` definition. The brush set/setting spray animate without changing persistent appearance. Reloading a saved appearance, a clothing change or relogging can replace the overlay; no appearance persistence integration is supplied.

### An animation looks wrong

Review candidate clips and prop transforms in `data/poses.lua` and `data/products.lua`. Application uses available game hand-to-face animations. The machine's player stand/camera/START offsets are in `Config.Mixer`; they must match the supplied prop geometry.

## Sales, Progress and Storage

### Buyers do not arrive or pop-up pauses

Carry a supported cosmetic and respect cooldowns. The pop-up flow pauses during menus, crafting, vehicles, death and an active thief. It stops when stock runs out. NPC pathfinding depends on the location; arrival timeout moves a stuck buyer closer. Check networked entity settings and server restrictions if buyers cannot spawn.

### A stolen product cannot be recovered

Search the downed thief after the sale finishes and before the record expires. Expiry starts at sale creation, and stolen records do not persist through restart. Free inventory room before recovery. The normal flow prevents starting another sale while the thief is active.

### Goals, awards or leaderboard look stale

Goals reset daily at UTC midnight and weekly on Monday at 00:00 UTC; unclaimed old goals are replaced. Awards require an explicit claim. The leaderboard caches rows for 60 seconds and ranks NPC earnings including tips, excluding goal/award cash. Close and reopen the hub for refreshed profile data.

### Progress or equipment disappeared after an update

Check the selected backend and preserved `storage/data.json` or SQL tables. Merely switching JSON to SQL creates a separate store. Ensure `storage/` exists and is writable. Migration is disabled by default; enabling it does not overwrite existing GlowUp profiles. Review the source backend and one-time placement marker in [Integrations](integrations.md#legacy-studio-import).

### Webhooks do not send

Set the appropriate URL in `server/config.lua` and restart. Empty channels are disabled. Check the host's outbound HTTP access. The admin channel reports specific rejected requests rather than every error.

## Interface

Missing WebP item art falls back to `inventory/<item>.png`, then a monogram. Keep those PNGs in the resource even after copying icons into an external inventory. Check client caching after replacing images or translations.

The release ships its built HTML/CSS/JavaScript. Developers should work from the original source checkout when changing the interface and rebuild the release assets. This workspace review does not replace a live FiveM test of coordinates, animation timing, models, framework APIs and networked NPC behaviour.
