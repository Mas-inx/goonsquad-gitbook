# Troubleshooting

Start with `/gsdiag`. It answers most save and identity problems in one command, and it works even when admin detection is misbehaving. Capture the complete `[gs_appearance]` lines from the server console and the player's F8 console before opening a ticket.

---

## Startup

### `oxmysql is not running`

Nothing saves or loads without it. Start it first:

```cfg
ensure oxmysql
ensure gs_appearance
```

### The conflict banner appears

```text
[gs_appearance] CONFLICT: illenium-appearance still running.
```

Another appearance resource still owns the ped. Remove it from `server.cfg` — the `provides` block satisfies other scripts' dependency checks, but it does not stop a resource that is still ensured. Leaving both running is what causes double menus, resets, and lost outfits.

The check ignores names that merely alias to GS Appearance, so this banner never fires on a clean server.

### The banner says `framework: standalone`

```text
[gs_appearance] No framework detected (qbx_core / qb-core / es_extended / ox_core).
```

Appearances then save against the license identifier and job features are off.

1. Start `gs_appearance` **after** your framework.
2. Confirm the framework resource is actually named `qbx_core`, `qb-core`, `es_extended`, or `ox_core`.
3. Set `Config.Framework` explicitly if your server runs an unusual combination.

A framework that starts later is normally picked up automatically, so a persistent `standalone` means it never started or has a different resource name.

### The wrong framework is detected

On a Qbox server that also runs `qb-core` as a legacy shim, Qbox is chosen deliberately — only `qbx_core` owns the player there. If your server genuinely wants the other adapter, set it:

```lua
Config.Framework = 'qb'
```

### `ensure gs_appearance` hangs or trips `svMain seems hung`

That burst comes from registering the ~7,900 preview images in the `files{}` block on every start. The shipped manifest deliberately leaves the `default_captures/**/*` glob out and loads previews from the CDN instead.

If you added that glob back, remove it and set:

```lua
Config.CaptureSource = 'fivemanage'
```

See [Installation](installation.md#5-preview-images).

---

## Saving and Identity

### Appearance or outfits are not saving

Run `/gsdiag` as the affected player. The decisive line is:

```text
[gsdiag] citizenid      : ABC12345
[gsdiag] outfit write   : OK
```

| Result | Meaning |
|---|---|
| `IDENTITY UNRESOLVED` | The framework has no player object for that source. Nothing can save until this returns a value |
| `DB READ FAILED` | The database account cannot read the tables |
| `outfit write : FAILED` | The write itself failed — read the error, usually a permission or schema problem |
| Everything `OK` | Saving works; the problem is elsewhere |

An unresolved identity usually means the character is not fully loaded, or a multicharacter resource has not handed over yet.

### Outfits save but disappear from the list

Every save is verified by reading the row back, and a just-saved outfit is held in a session cache so it cannot vanish from the list. If an outfit is genuinely missing, check `/gsdiag` for the `player_outfits` row count and confirm the citizen id matches the one in the table.

An outfit saved under a name that already exists updates that outfit rather than adding a second one. That is intentional.

### ESX outfits were lost or mixed up

An ESX Legacy identifier is 54 characters and overflowed the old `VARCHAR(50)` `citizenid` column, which truncated it and could cross ownership. Startup widens that column to `VARCHAR(64)` automatically. Confirm the column width if you are on a hand-imported schema.

### `users`.`skin` is not being written on ESX

That column is the mirror, not the source of truth. GS Appearance does not create it — `es_extended`'s own player query selects it, so it exists on any working ESX server. If it is missing, the mirror is skipped with a notice and `playerskins` is still written correctly.

---

## The Character Creator

### It opens on every join

A returning player should never see it.

1. Run `/gsdiag`. If `creator opens` says `YES`, that player genuinely has no active `playerskins` row.
2. If the identity is unresolved, fix that first — an unresolved identity used to be the classic cause, and identity resolution now retries for up to 10 seconds before answering.
3. If you run a multicharacter **fork** that raises its first-character event on every login, set:

```lua
Config.TrustFrameworkNewCharacter = false
```

Leave it `true` on stock `qbx_core`, `qb-multicharacter`, and `esx_multicharacter`.

### It never opens for a new character

1. Confirm the multicharacter resource actually raises its first-character event.
2. On ESX, confirm `esx_multicharacter` or `esx_identity` is running; without either, the creator opens from `esx:playerLoaded` after waiting up to 15 seconds for one of them to claim it.
3. With `Config.TrustFrameworkNewCharacter = false`, a database lookup decides instead, and a failing lookup silently skips the creator. Set it back to `true`.
4. Open it manually as an admin with `/gscreatechar` to confirm the creator itself works.

### Existing players were sent back through the creator after installing

They should not have been — a row left by `qb-clothing` or `esx_skin` counts as "has an appearance" and is converted instead.

Confirm the predecessor's rows are still in `playerskins` (or `users`.`skin` on ESX) and were not dropped during the switch. See [Integrations](integrations.md#migrating-from-a-predecessor).

### Character select shows the right clothes on the wrong ped (QBCore)

Known limitation. `qb-multicharacter` reads `playerskins.model` with `tonumber()` and cannot resolve the model name stored there. In game the player spawns correctly; only the select preview is affected. Storing a hash instead would break Qbox, which reads the same column with `joaat()`. `illenium-appearance` has the same trade-off.

---

## Preview Images

### Tiles are blank or show placeholder icons

Run `/gscapcheck` in game. It prints how this client resolved previews:

```text
[gscapcheck] source=fivemanage baseUrl=https://r2.fivemanage.com/abc123
[gscapcheck] sample base_0 -> https://r2.fivemanage.com/abc123/default_captures/…/base_0.png
```

Open the printed sample url in a browser. If it 404s, the images are not where `Config.CaptureBaseUrl` says they are.

| Symptom | Cause |
|---|---|
| `baseUrl=none` and `manifestEntries=0` | Neither a base url nor a url map. Set `Config.CaptureBaseUrl` |
| Sample url 404s | Wrong base url, or the upload never completed. Re-run with `--fresh` |
| `source=local` with no images | `default_captures/` was deleted while `Config.CaptureSource` is `local` |
| Url resolves in a browser but tiles are still blank | The CDN is unreachable from the player's network, or the hosting account is out of capacity |

The server also warns at startup when `CaptureSource` is `fivemanage` with no base url and no map:

```text
[gs_appearance] Config.CaptureSource is "fivemanage" but no Config.CaptureBaseUrl is set
```

With no base url set, the per-file map is used instead. A newly connected player and a player who was already online when the resource restarted can behave differently there, because a client only receives resource files it was sent when it joined — the server hands the map over a callback for exactly this reason, so a rejoin is a useful test. Setting a base url avoids the whole problem.

### Previews still load from the seller's CDN after uploading

The resource ships with a complete url map, so a plain upload run skips every file as "already mapped" and prints:

```text
Nothing to upload — every image is already in the manifest.
```

Re-run with `--fresh`, then paste the base url the run prints into `Config.CaptureBaseUrl`.

### An icon override does not appear

1. Confirm the key matches one of the names in [Configuration](configuration.md#icon-overrides) — an unknown key is ignored.
2. Confirm `Config.CustomIcons` is `true`. With it off, images are disabled entirely and overrides do nothing.
3. For a resource-relative path, confirm the file is covered by the `files{}` block. `web/images/` is; a new top-level folder is not. Note the path is `web/images/...`, not `images/...`.
4. Confirm you did not put the file in `web/dist/` — `npm run build` empties that folder.
5. Restart the resource — the override map is pushed to the NUI on start.

An override that fails to load falls back to the bundled icon, so a broken path looks like "nothing changed" rather than a broken image.

### Barber or tattoo previews fall back to icons

`default_captures/appearance/preview_index.json` maps a style number to its image name and is read from disk in **both** local and CDN modes. If you trimmed the capture folder, keep that file.

Regenerate it after adding your own PNGs:

```bash
node tools/build_preview_index.js
```

### The upload tool fails

- Pass the key as `FIVEMANAGE_API_KEY` in the environment; the tool never reads it from a file.
- Run it from the resource root, the folder containing `fxmanifest.lua`.
- Node 18 or newer is required.
- The tool is resumable — re-run the same command after a rate limit or a dropped connection.
- Use `--dry-run` to check what it would do without a key or a network call.

---

## Stores

### No prompt appears at a store

1. Confirm the store is enabled in `/gsadmin`.
2. Confirm you are inside its radius; the default is 3.0 meters.
3. If the store has a job or gang lock, confirm your job and grade match.
4. Confirm `Config.StoreInteractControl` is not bound to something your server overrides.

### The store opens for players who should be locked out

Job locks match the player's **job or gang** against each entry's name and minimum grade. An empty job list makes the store public. Confirm the entries were saved with a name, not left blank.

### Blips are missing

`Config.StoreBlip.enabled` is the global switch. Per-type defaults come from `Config.ShopBlips`, and each store can override sprite, color, scale, and visibility from the admin map. A disabled store never draws a blip.

### A store was deleted by accident

Store zones are database rows shared by the whole server, and deletion is immediate. Recreate it from `/gsadmin` with **Use my position**, or restore the row from a database backup. Disable stores instead of deleting them when you may want them back.

---

## Admin

### `/gsadmin` does nothing

Admin is checked on the server. A player passes if **any** of these match:

- an identifier in `Config.Admins`
- the `gs_appearance.admin` ACE
- a Qbox or QBCore permission from `Config.QBPermissions`
- an ESX group from `Config.ESXGroups`

Grant the ACE and restart:

```cfg
add_ace group.admin gs_appearance.admin allow
```

On a standalone server, framework permissions do not exist, so the ACE or `Config.Admins` is the only route.

### An item rule locks out everyone

A `job_lock` on an item denies anyone who does not match a listed job or gang. That is what it is for — you do not also need a blacklist.

If a rule locks out people it should not:

1. Check for a second rule on the same category and drawable. Precedence is player whitelist, then job locks, then blacklist.
2. Confirm the job names match the framework's own names exactly.
3. Confirm the minimum grade is not higher than any real grade.

A `job_lock` with no jobs and a `player_whitelist` with no identifiers are rejected on save, because both would lock the item for everyone.

### The accent color reverted

The accent is stored server-side and pushed to everyone. `Config.AccentColor` is only the value used before any admin has set one. Once an admin picks a color, that choice wins until it is changed again.

---

## In the Editor

### A player is stuck with NUI focus or a frozen ped

```text
/gsrelease
```

It restores NUI focus, ped control, the camera, and the screen fade without a reconnect. It is also available as the `ForceRelease` export, so a HUD or spawn script can call it.

### Hair turns green

Hair is always routed through `setPedHair` so it keeps its tint, and component `2` is protected from `setPedComponent` on freemode peds. If another resource is setting component 2 directly, that is where to look.

### Props stay stuck on the ped

```lua
TriggerClientEvent('illenium-appearance:client:ClearStuckProps', source)
```

### The face resets to defaults after saving from a shop

It should not. The editor seeds itself from the live ped once and then hands authority to the UI, so a barber save cannot flatten a sculpted face. If you can reproduce it, note which shop and which section were open and include that in a ticket.

### Saving charges the player twice

A shop charges once per visit, even across several sections of the same menu. Tattoo shops with `Config.ChargePerTattoo = true` bill each tattoo instead, which is expected.

---

## Third-Party Scripts

### A script calling `illenium-appearance` exports gets nothing back

1. Confirm the old resource is stopped — if it is still running, it may win the export name.
2. Use the colon form, `exports['illenium-appearance']:setPedAppearance(ped, appearance)`. The dot form shifts the first argument.
3. Confirm the export name is on the documented surface in [API & Exports](exports.md).

### `lib.callback.await('illenium-appearance:server:…')` times out

The ox_lib callback protocol is reimplemented and re-claimed when `ox_lib` starts, so it works with or without ox_lib installed. If a call still times out, confirm the callback name is one of the documented ones and that the caller is using the `illenium-appearance:server:` prefix.

### A spawn script hangs on a black screen

Every ESX spawn round-trip fires its callback unconditionally, so `skinchanger:loadSkin` and `esx_skin:getLastSkin` cannot leave a caller waiting. A black screen that persists points at the spawn manager itself; test with `/gsrelease` to confirm GS Appearance has released focus.

---

## Information to Send Support

- GS Appearance version
- Server artifact version and framework (Qbox / QBCore / ESX / ox / standalone)
- The appearance resource you migrated from, if any
- `Config.CaptureSource` and the `/gscapcheck` output
- The full `/gsdiag` output for the affected player
- The complete server console output from resource start, including the ready and conflict banners
- Any F8 client console errors
- The exact action that reproduced the issue

Remove database credentials, API keys, and player identifiers before sharing logs publicly.
