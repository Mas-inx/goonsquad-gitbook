# Troubleshooting

Set `Config.Debug = true` while investigating, reproduce the problem once, and capture the complete `[gs-vehicledesigner]` and `[gsvd]` lines from both the server console and the player's F8 console.

---

## Startup and Database

### `oxmysql exports are not available`

Start `oxmysql` before the designer:

```cfg
ensure oxmysql
ensure gs-vehicledesigner
```

Confirm the database credentials are valid and that `oxmysql` reached the database before the designer initialized.

### Designs are not saving

The tables are created on boot, so a failed migration usually points at database permissions. Check the server console for MySQL errors during startup and confirm the account may create tables in that schema.

### The pool never finishes booting

The vehicle pool waits for the database migration to finish. If the event never fires, it polls for the base table and gives up after roughly 30 seconds with a clear error. That error almost always means `oxmysql` is not connected.

---

## Vehicles and the Pool

### No vehicles appear in the studio

1. Confirm files are under `gs-vehicledesigner/vehicle_templates/`.
2. A model is only discovered when a `vehicles.meta` in the tree declares it **and** the matching `<model>.yft` plus `<txdName>.ytd` exist somewhere in the tree. A model with metas but no stream files, or stream files with no meta, is skipped.
3. Run `gsvd_rescan` and read its summary.
4. Run `gsvd_pool_status` to see what the resource actually provisioned.

### New vehicles do not stream after the first boot

The first boot that converts a vehicle writes the generated files, then prints a restart banner. FiveM only mounts those files on the next start.

```text
[gs-vehicledesigner] New vehicle assets were materialised.
Restart the server for the vehicles to take effect.
```

Restart once more. Later restarts are instant because unchanged vehicles are skipped by content signature.

### A changed template was not reconverted

Each vehicle is signed at `data/<model>/signature.json`. If a template changed but was not picked up, delete that model's `signature.json` and restart, or run `gsvd_rebuild_pool`.

### A vehicle shows as locked or full

Every model has 16 live slots. When the model and all of its series clones are occupied, the card locks and the next rebuild mints a new series clone automatically.

- Delete unused published designs to free slots immediately, with no restart.
- Run `gsvd_expand <model>` to force the next clone now, then restart so it streams.
- Remember a clone is a separate spawn code: designs on `viper_2` fit `viper_2`, not `viper`.

### Paint does not cover part of the body

The converter reports any paint-family shader it cannot convert, per vehicle, at boot. A body part using an unsupported shader keeps its original material and cannot receive a livery. Capture the conversion warnings for that model when reporting it.

### A car pack's tuning parts or wheels are missing

Extra streamables are attached to the nearest model folder that contains a model YFT. A flat folder holding several models' YFTs cannot attach parts by folder, so only `<model>*` name prefixes are matched there. Use a per-model folder layout for packs with shared, ambiguously named parts.

---

## Inventory

### Livery item definition was not found

The item in `Inventory.itemName` does not exist in the detected inventory backend.

1. Install the matching snippet from `gs-vehicledesigner/install/`.
2. Confirm the item name is identical in both files.
3. Restart the inventory or the server so its item list reloads.
4. Confirm the intended inventory resource starts before the designer.

### Using the item does nothing

1. On `ox_inventory`, confirm the item definition still carries `client.export = 'gs-vehicledesigner.useLiveryItem'`.
2. On AK47 Inventory, confirm the `server.onUse` callback from the install snippet is intact.
3. Every other backend uses a server-side usable handler that is registered about a second after startup; confirm the resource started cleanly.

### "This livery fits X, not the nearby vehicle"

Printed items are locked to the model they were printed for, including series clones. Check the item metadata against the vehicle you are standing next to.

### The item is consumed but nothing happens

The target vehicle must be networked. A locally spawned, non-networked vehicle is rejected before anything is consumed. If `requirePlayerOwnedVehicle` is enabled, the plate must also exist in the framework's owned-vehicle table.

---

## Media and Item Images

### Item images are blank or show only the fallback icon

1. A real vehicle render requires `AssetUploadProvider` set to `discord` or `fivemanage`. The `database` provider produces no public URL.
2. Confirm the matching credential is set in `server/credentials.lua`.
3. With `ox_inventory`, the image host must be allowed in ox's `inventory:validhosts` convar.

### `Media upload request failed` with FiveManage

1. `AssetUploadProvider` is exactly `'fivemanage'`.
2. `GSVD.Credentials.FiveManageApiKey` contains an active token with file-upload access.
3. The default `base64Endpoint` has not been replaced with a dashboard or CDN URL.
4. The server can make outbound HTTPS requests to FiveManage.
5. The account has storage and request capacity available.
6. `retentionExempt` is permitted for that token; it ships as `true`.

The endpoint is already supplied:

```text
https://api.fivemanage.com/api/v3/file/base64
```

With debug enabled the log includes the returned HTTP status. Include it in a ticket, with the API key removed.

### Discord upload fails

- Confirm the full webhook URL is in `server/credentials.lua`.
- Only genuine Discord webhook hosts are accepted; a proxy or shortener URL is rejected.
- Confirm the webhook still exists and can post in its channel.
- Confirm `AssetUploadProvider = 'discord'`.

Rotate a webhook if it ever appears in logs, screenshots, or a repository.

### Uploads time out or return the busy message

Vehicle renders are large, and base64 inflates them further. The `upload` queue bounds how many run at once.

- A full pending queue returns `busyMessage` immediately.
- Raising `maxConcurrent` reduces waiting on a strong host but increases CPU, bandwidth, and provider pressure.

### AI generation fails with a provider message

AI images need a hosted provider. With `AssetUploadProvider = 'database'` the resource refuses to host generated media and says so directly. Model execution, timeouts, and keys belong to the companion resource, not to the designer.

---

## The Studio

### The studio does not open

1. Check F8 and the server console for errors.
2. If `Designer.requireStation = true`, confirm the player is inside a station's `interactDistance`.
3. If `Access.allowEveryone = false`, confirm the player has the ACE, a listed job and grade, admin access, or an active pass.
4. Run `gsvd_whoami <serverId>` from the server console to see exactly how the resource evaluates that player.

### The studio opens with no vehicle selected

The studio only preselects a vehicle that exists in the converted pool. A car streamed by another resource, but never placed in `vehicle_templates/`, is not recognised. With `RequireDriverSeat = true` the player must also be in the driver's seat.

### Painting does nothing on part of the model

Only converted paint surfaces accept paint. Glass, interior meshes, and unconverted materials deliberately block brush input. Switch to **UV Layout** to confirm where the design is landing.

---

## Liveries in the World

### The finish does not appear after fitting

1. Other players only see finishes within `Runtime.renderDistance`.
2. Check F8 for runtime texture binding lines; binding retries run for `retryWindowMs`.
3. Confirm the design is still published. Deleting or unpublishing releases the slot and the finish stops resolving.

### The livery disappears after a restart

Only owned vehicles persist. A vehicle whose plate is not in the framework's owned-vehicle table receives a non-persistent finish that is gone once the entity is.

Confirm the row exists in `gs_vehicledesigner_vehicle_liveries` and that the vehicle respawns with the same plate.

### An edited design changed an already printed item

It should not. Printed items store the exact version they were printed at, and fitting always applies that version. If you see otherwise, capture the design ID, the item metadata, and the version numbers involved.

---

## Tebex

### Package grant says `package_not_found`

The command argument must match a package config key, `TebexPackageId`, `TebexPackageName`, or `Label`. Config keys are the least ambiguous.

### Offline player cannot claim

The identifier passed by Tebex must exactly match the framework identifier, or another identifier FiveM reports for that player when online. Test with an online source first, then inspect the stored `owner_identifier` in `gs_vehicledesigner_tebex_entitlements`.

### `package_used` with AI generations left

This is expected with the recommended rule:

```lua
RequireRemainingLiveriesToClaim = true
```

Once livery prints reach zero, leftover AI does not keep the pass claimable.

### Purchase granted twice

Pass a unique transaction value as the third command argument. Duplicates are only detected when the same package key and the same non-empty transaction ID are supplied.

---

## Information to Send Support

- GS Vehicle Designer version
- Server artifact version and framework (QBCore / Qbox / ESX / standalone)
- Inventory resource name
- Media provider (`database`, `fivemanage`, or `discord`)
- The full server console output from resource start, including the pool boot lines
- Any F8 client console errors
- The affected vehicle model and, for conversion problems, its template layout
- Exact action that reproduced the issue

Remove API keys, webhooks, database credentials, and player-private identifiers before sharing logs publicly.
