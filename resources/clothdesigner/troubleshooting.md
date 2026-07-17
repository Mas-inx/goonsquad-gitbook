# Troubleshooting

Enable `Config.Debug = true` while investigating, reproduce the problem once, and capture the complete `[gs-clothdesigner]` lines from both the server console and the player's F8 console.

---

## Startup and Database

### `oxmysql exports are not available`

Start `oxmysql` before Cloth Designer:

```cfg
ensure oxmysql
ensure gs-clothdesigner
```

Confirm the database credentials are valid and `oxmysql` reached the database before Cloth Designer initialized.

### Custom clothing item definition was not found

The item in `Config.InventoryItemName` does not exist in the detected inventory backend.

1. Install the matching snippet from `gs-clothdesigner/install/`.
2. Confirm the item name is identical in both files.
3. Restart the inventory or server so its item list reloads.
4. Confirm the intended inventory resource starts before Cloth Designer.

---

## Templates and Pack Generation

### No silhouettes appear

- Confirm templates are under `cloth_templates/<gender>/<category>/`.
- Confirm the gender and category names match configured folders.
- Run `gscd_rescan` from the server console and read its summary.
- Restart after materializing new generated apparel.

### First-start clothing does not appear

The first start writes the generated apparel pool. FiveM then needs another restart to register it. Follow the printed restart message and restart once more.

### `references unknown` or pack generation fails

This means a drawable or texture reference inside the source assets could not be resolved safely. Common causes include:

- A YDD expects a companion YTD that is missing.
- A YTD does not contain the texture name referenced by the YDD.
- The YDD or YTD was renamed without updating its internal relationship.
- A stale generated assignment points to a source file that changed or was removed.
- The asset is damaged or uses an unsupported structure.

The 1.3.0 generator logs the exact source YDD, expected companion YTD, and generated YDD involved. It skips a stale invalid assignment so valid pack items can continue. Use the printed file names to inspect or replace the source pair, then rescan and rebuild or restart.

When requesting support, include the full diagnostic block. A message such as only `references unknown` does not identify the failing asset.

### Template shows full

Each generated drawable has 26 texture variants. Cloth Designer can allocate another drawable, but generated apparel still needs to be materialized and registered. Restart the server and follow any additional restart message. `gscd_rebuild_pool` is available for advanced maintenance.

---

## Uploads and Media

### File is too large

`MaxUploadBytes` defaults to 4 MB. In 1.3.0 the NUI checks file size before `FileReader`, so the player should immediately see that the image exceeds the allowed size. The server performs a second check for security.

If the warning does not appear, confirm clients received the latest `web/dist` build and clear any stale FiveM NUI cache before retesting.

### `Media upload request failed` with FiveManage

Check the following:

1. `AssetUploadProvider` is exactly `'fivemanage'`.
2. `GSCD.Credentials.FiveManageApiKey` contains an active token with file-upload access.
3. The default `base64Endpoint` has not been replaced with a dashboard or CDN URL.
4. The server can make outbound HTTPS requests to FiveManage.
5. The FiveManage account has storage and request capacity available.
6. The image is PNG, JPEG, or WebP and below both size and dimension limits.
7. The configured `path` and `retentionExempt` values are allowed for that token.

The endpoint is already supplied by Cloth Designer:

```text
https://api.fivemanage.com/api/v3/file/base64
```

The server owner normally finds only the API token in the FiveManage dashboard. They do not need a unique endpoint. With debug enabled, the server log includes the returned HTTP status and response body; include those lines in a support ticket but remove the API key.

### Discord upload fails

- Confirm the full webhook URL is in `server/credentials.lua`.
- Confirm the webhook still exists and can post in its channel.
- Check that Discord is reachable from the host.
- Confirm `AssetUploadProvider = 'discord'`.
- Create a new webhook if the old one was exposed, deleted, or rate-limited persistently.

Never post a live webhook publicly. Rotate it if it appears in logs, screenshots, or a repository.

### Upload or generation times out

Base64 temporarily increases payload size, while image decoding, remote HTTP requests, and generated previews add latency. Cloth Designer uses latent callbacks and a bounded upload queue so simultaneous requests do not all run at once.

Check `Config.Queues.Upload`:

- A full pending queue returns `BusyMessage` immediately.
- `MaxConcurrent = 1` protects the host and media provider but makes later jobs wait.
- Increasing concurrency can reduce waiting on a strong host, but it also raises CPU, memory, bandwidth, and provider pressure.

AI model execution belongs to the registered studio-tool resource. Inspect that resource's timeout and queue as well. Cloth Designer's studio-tool request waits up to 270 seconds, while its generated reference and result hosting use the media queue.

---

## Designer UI

### UI pauses when opening

The 1.3.0 UI mounts only library content near the visible scroll region and unloads previews that move far off-screen. It also manages 3D renderer lifecycle. A short initial cost can still occur while the first visible images, fonts, and selected model load.

If the pause is severe:

- Confirm the latest `web/dist` was shipped with the resource.
- Check F8 for missing files or repeated network errors.
- Check template preview file sizes.
- Close other NUI-heavy resources and compare.
- Update GPU drivers and test without graphics injectors or overlays.

### `Too many active WebGL contexts. Oldest context will be lost`

Older builds created more 3D renderers than the Chromium NUI context limit allowed. When the limit was reached, Chromium destroyed the oldest context, leaving a blank or broken preview.

The 1.3.0 UI reuses and disposes WebGL renderers and virtualizes off-screen previews. If the warning continues after updating:

1. Confirm no old `web/dist` files were mixed into the release.
2. Clear the client cache and reconnect.
3. Identify whether another NUI resource is also creating many WebGL contexts.
4. Capture the first warning and the actions immediately before it.

### Preview background does not stay selected

The black/light-gray selection is stored in the NUI browser's local storage. Clearing FiveM cache or browser storage resets it to black. Confirm the NUI can use local storage and that another script is not clearing it.

---

## Wearing and Persistence

### Item equips but does not unequip on second use

The same printed design should toggle off on second use. Confirm both the server and web/client files are from 1.3.0, and make sure only one `gs-clothdesigner` resource copy is started. Mixed versions can leave the inventory and wardrobe state out of sync.

### Clothing disappears for a second and returns

This usually indicates stale persisted wearable state or a delayed retry from an older build. The 1.3.0 flow invalidates stale retries and synchronizes inventory and wardrobe actions. Update the complete resource, restart it, then use the item or wardrobe to remove the design again.

### Shoes do not restore correctly

The active-wearable row stores the previous appearance for footwear and other drawable types. Confirm the database contains the `previous_appearance_json` column and startup migrations completed. Test using a supported freemode ped and ensure an outfit resource is not immediately reapplying shoes after Cloth Designer removes them.

### Pants flicker when another item is equipped or removed

GTA can retain a stale local material cache for component `4` (pants). The runtime prepares textures before applying clothing and allows one delayed hard fallback for pants. Keep this default:

```lua
postApplyHardReloadComponents = {
    [4] = true,
}
```

Do not add every component to this list without a demonstrated cache problem; unnecessary hard reloads can make outfit changes more visible.

### Clothing does not return after reconnecting

1. Confirm the design remains in `gs_clothdesigner_active_wearables`.
2. Confirm the player rejoins with the same framework identifier.
3. Confirm the ped is `mp_m_freemode_01` or `mp_f_freemode_01`.
4. Check that the framework's player-loaded event fires.
5. Check for an appearance resource that applies the base outfit after Cloth Designer's restore retries.
6. Look for `Preparing runtime texture`, `Bound runtime texture`, and `Applied ... variation` lines in F8.

Shoes appearing while other components do not usually means the database state was restored but the local material or outfit order differs by component. Capture all component lines, not only the final success notification.

### Wardrobe and item disagree about equipped state

Both paths use the same server wearable records in 1.3.0. Update all files together, restart the resource, and verify there is only one resource copy. Avoid directly editing `gs_clothdesigner_active_wearables`; use the item, wardrobe, or clear event.

---

## Tebex

### Package grant says `package_not_found`

The command argument must match a package config key, `TebexPackageId`, `TebexPackageName`, or `Label`. Config keys are the least ambiguous.

### Offline player cannot claim

The identifier passed by Tebex must exactly match the framework identifier or another identifier reported by FiveM when that player is online. Test with an online source first, then inspect the stored `owner_identifier` in `gs_clothdesigner_tebex_entitlements`.

### AI remains but the player cannot claim or generate

This is expected when both recommended rules are enabled:

```lua
RequireRemainingComponentsToClaim = true
RequireRemainingComponentsForAI = true
```

Once clothing uses reach zero, leftover AI does not keep the pass claimable and cannot be spent later.

### Purchase granted twice

Pass Tebex's unique transaction value as the third command argument. Cloth Designer de-duplicates a repeated transaction only when the same package key and non-empty transaction ID are supplied.

---

## Information to Send Support

- GS Cloth Designer version
- Framework and inventory resource names
- Media provider (`database`, `fivemanage`, or `discord`)
- Complete server console error block
- Complete relevant F8 block
- Exact action that reproduced the issue
- Source YDD/YTD names for template problems
- Whether the problem happens before or after reconnecting

Remove API keys, webhooks, database credentials, and player-private identifiers before sharing logs publicly.
