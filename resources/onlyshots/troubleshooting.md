# Troubleshooting

Check the server console and client F8 console first. Enable `Config.Debug = true` temporarily for extra adapter and XP logging, then restart `gs-onlyshots`.

A normal startup prints the detected framework/inventory/target, persistence mode and image backend, ending with:

```text
[gs-onlyshots] OnlyShots ready | items: gs_camera / gs_photo / gs_bundle | photo storage: sql
```

---

## Installation and Access

### The app opens but creator actions are refused

The current ped model must match `Config.Gate.models` when `femaleOnly` is enabled. The default is `mp_f_freemode_01`. Framework gender or a job name alone does not grant access. Add your custom model to the list or adjust the model restriction.

If the role gate is enabled, check the guild/role/token fields in `server/sv_config.lua`, the player's Discord identifier and the bot's guild-member lookup. An incomplete config or failed lookup denies access. Cached results last ten minutes by default.

### The boutique ped or E prompt is missing

- Confirm `Config.Shop.coords` matches your map and has the correct standing ped height.
- Check `Config.Shop.ped` is a valid model and the resource started cleanly.
- The E prompt appears only inside `Config.Shop.distance` when `Config.Target = 'none'` or no target was detected.
- If a target was detected, use its interaction instead, or force `'none'` and restart OnlyShots.
- Close the app and camera while testing proximity prompts.

### Buying the camera fails

Check the selected cash/bank account, camera price and external item definition. A player cannot buy another camera while already owning one. A failed inventory addition refunds the purchase price.

Standalone wallets start at zero. For testing, set `Config.Shop.price = 0`, or connect your economy in the open server bridge.

### Using an inventory item does nothing

Verify the configured item names, usable flags, start order and item hooks. ox_inventory needs `server.export = 'gs-onlyshots.useCamera'` or the corresponding photo/bundle export. QB/Qbox/ESX use framework registration. Restart the full server when shared item definitions were changed.

### ESX photo items have no metadata or never appear

Registering the names in an ESX SQL items table does not implement photo metadata support. Use ox_inventory or `Config.Inventory = 'internal'`, or implement an adapter in `server/inventory.lua`. Internal items appear in the OnlyShots app rather than the default ESX inventory.

### A command conflicts with another script

Rename or disable its entry in `Config.Commands`. F7 and H are separate key mappings and can be rebound by players. Setting the studio chat command to `false` does not disable the studio keybind.

---

## Images

### Photos show artwork instead of the screenshot

- Start the resource named by `Config.Upload.resource` before OnlyShots, default `screenshot-basic`.
- Check `Config.Upload.storage`; `'none'` deliberately uses artwork.
- Check host credentials in the server config for Fivemanage/Discord/custom modes.
- Restart OnlyShots after fixing capture start order or host readiness so the app receives updated configuration.
- Inspect upload warnings in the server console and capture errors in F8.

The value, caption and gameplay can still work when there is no real image.

### `rejected photo upload ... (bad or oversized image)`

`Config.Upload.maxKB` limits the entire encoded image data URL. Base64 adds size beyond the JPEG's binary size. Lower `width` or `quality`, or raise the limit. The current UI clamps quality to 0.5–0.95 and the configured resize side to at least 320.

### Images disappear after a restart

With SQL image mode and no running database adapter, images are stored only in memory. `data/store.json` saves progress/internal items but not image data. Start a supported database adapter or configure a hosted backend for future photos. Lost memory-only images cannot be restored from item metadata alone.

### An image disappears after an NPC sale or escaped theft

SQL image deletion is intentional when the photo or collection is consumed by an NPC sale or permanently lost after theft. The collection's contained SQL images are deleted too. Player trades preserve images.

### Fivemanage upload fails

Check `ServerConfig.Upload.fivemanage.token`, the configured v3 base64 endpoint, server outbound HTTPS access and the HTTP status warning. The release expects JSON containing `url` or `data.url` with an HTTPS image URL. Keep the API key on the server.

### Discord upload fails or a hosted link stops working

Check the upload webhook in `ServerConfig.Upload.discord.webhook`. It is separate from `ServerConfig.Webhook.url`, which is only the activity feed. Inspect the console's upload response. The host controls attachment link availability; OnlyShots does not renew saved links.

If the console reports that `server/upload.js` is not loaded, refresh resources and restart OnlyShots with the complete manifest and JavaScript uploader present.

### Database images do not show in inventory tooltips

SQL photos store `shot:<id>`, not an HTTPS URL. The OnlyShots app resolves these references itself. Hosted photos can supply tooltip `imageurl` metadata, depending on the inventory. Check images in **My content** or the item viewer first.

### The activity webhook posts text without an image

Only HTTPS-hosted images are attached to the feed. SQL image references are not external URLs. The feed and image hosting are configured independently.

---

## Sales, Collections and Trades

### No buyer can be called

Check that the player passes creator access, owns sellable photo/collection metadata and has no active sale or theft. The default buyer cooldown is 45 seconds from the last request. Collected work from another creator is not sellable to NPCs.

### The buyer is stuck, disappears or cancels

Test on an open street with accessible ground. Rooftops and obstructed interiors can interfere with NPC spawn/navigation. Check `spawnDistance`, `maxElevation` and `walkTimeout`. Buyers are moved closer after the walk timeout; leaving `cancelDistance` or dying before arrival cancels the sale. Unresolved requests also have a server timeout.

### The thief is dead but recovery fails

Search the body inside the recovery window, make inventory space, and stay within loot range. The server validates the synchronized thief entity, death and distance. Check OneSync/entity synchronization if the client and server disagree about that entity. Keep `Config.Thief.serverValidate = true` for normal operation; disabling it bypasses those checks.

### Photos cannot be added to a collection

Only your own single photos are eligible. Check the minimum in `Config.Bundle.min`, the maximum in the current rank's `bundleSize`, inventory metadata and whether the selected photos still exist. Collected work and nested collections are not eligible.

### Selling at a hotspot does not increase the payout

The hotspot factor is stored on capture/save, with the rank requirement checked then. Moving to a premium spot only to sell does not change an existing photo. Collections average the factors of their included photos.

### A trade offer does not send or fails on acceptance

Close the app, free-aim at the player's ped and press the mapped trade key. Check `Config.Trade.enabled`, range, offer cooldown, buyer funds and creator-owned items. Only one pending offer is held per seller. On acceptance, the server rechecks ownership, funds, range and inventory transfer.

### Bought photos cannot be resold to NPCs

Player purchases retain the original creator in `owner` and add collector information. They are collectibles for the buyer. This ownership rule also prevents using another creator's photos in your collections.

---

## Progress and Persistence

### The leaderboard does not match the wallet balance

It ranks lifetime NPC-sale earnings, including tips/viral payouts. Trades, challenge rewards and achievement rewards do not count toward that counter. Manual refresh has a default 30-second cooldown, and the list follows the configured model restriction.

### A completed goal cannot be claimed

Check that it is still in the current daily/weekly set and was not claimed already. Resets are 00:00 UTC daily and Monday 00:00 UTC weekly. A collection sale advances `bundles`, not single-photo `sales`. Reopen the app after a reset to get current state.

### Progress is missing after changing framework or character

QB/Qbox use `citizenid`, ESX uses its player identifier, and standalone uses platform identifiers. Framework or identity changes can point to a different saved row. Backend and inventory changes do not automatically migrate data. Restore/migrate from a backup while retaining metadata ownership relationships.

### Data does not persist

Check the startup persistence line, database connectivity/table permissions, or JSON path/write permissions. Preserve `data/` and the configured JSON file when updating. External inventory metadata is saved by that inventory, not copied into the internal item table.

For a backup, include the three `gs_onlypics_*` tables or `data/store.json`, plus external inventory item data. SQL images kept only in memory are not part of JSON backups.

### The resource was renamed and item use broke

Restore the folder name `gs-onlyshots`, or update every inventory export reference consistently. The supplied definitions target `gs-onlyshots.useCamera`, `usePhoto` and `useBundle`.

---

## Getting Support

Include resource version **1.0.0**, framework, inventory, persistence backend, image backend, relevant F8/server errors and reproduction steps. Remove tokens and webhook URLs from any configuration you share. Use [Support & Discord](../../support.md).
