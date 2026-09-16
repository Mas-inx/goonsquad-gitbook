# Troubleshooting

## The Editor Does Not Open

1. Confirm `gs-minimapdesigner` is started and inspect its server console errors.
2. Use the command configured in `Config.Command`; the default is `/minimapdesigner`.
3. Grant `minimapdesigner.edit` to a group the player belongs to, or add the player's complete identifier to `Config.Admins`.
4. Restart the resource after changing configuration.

The release's identifier list is empty. Turning off the ACE path does not grant access to everyone.

---

## The Interface Is Blank or Assets Are Missing

Keep the complete `web/build/` directory, including `index.html`, `assets/`, `map/`, `blips/` and `gametiles/`. The manifest points at this compiled directory.

The release does not need `web/src/`, npm or `node_modules`. If the server rejects the Node.js version, update the server artifact to one supporting the manifest's Node.js 22 runtime.

---

## The Editor Shows My Design but the Radar Does Not

- Enable **Bake onto radar tiles** in **Radar**.
- Use **Apply to my radar now** for a local preview, or **Publish** for all players.
- Wait for tile baking and transfer to finish.
- Verify `stream/minimap_main_map.gfx` is present.
- Disable competing resources streaming that same asset.

Text, images, postals, paths, polygons and heal patches need baked tiles. Native markers and circle/rectangle zones use a separate drawing path.

In version 0.2.0, automatic baking is triggered by a changed base or theme, enabled postals, images, or text. A sheet containing only paths, polygons or heal patches on an otherwise unchanged vanilla map may not trigger a bake; use a non-vanilla base or a visible theme adjustment for those sheets.

The message mentioning `extra-map-tiles v2` refers to an unavailable tile overlay. The renderer is already bundled; check the streamed asset and conflicts rather than installing a second copy.

### It Works on the Pause Map but Not the Small Radar

Check `Config.TilesOnRadar`. A value of `false` intentionally shows baked imagery only on the pause map.

### Tiles Are Clipped in Vehicles

Keep `Config.FlatRadar = true` and check whether another HUD continuously forces radar tilt. The live renderer uses a flat radar to avoid clipping.

---

## Saved Work Is Missing After Restart

Confirm which storage backend started. If `oxmysql` was missing, saves used JSON even when `Config.Storage` was set to `mysql`.

Check resource-folder write permissions, the selected database, table name and `data/active.json`. Saving a draft does not change the published pointer; publish the intended sheet.

For updates, preserve the existing database and `data/` directory. Do not overwrite live data with the clean release's default files.

---

## A Custom Map Pack Does Not Appear

- Place YTDs directly in `custom_maps/<pack>/`.
- Use a pack folder name of 1–64 letters, numbers, underscores or hyphens.
- Restart the resource after adding or changing a pack.
- Verify the internal texture names follow the `minimap_<row>_<col>` or `minimap_sea_<row>_<col>` pattern.
- Check the client console for texture parsing or fetch errors.

---

## Postals Do Not Route or Display

Enable postals in the applied sheet and publish it. Use the exact displayed code, including its prefix and padding.

The HUD also requires `Config.PostalHud = true`. Positions outside the grid return no postal. If another resource registers `/postal`, resolve the command conflict through the editable `client/postals.lua`.

---

## Media Is Not Uploaded or an Image Disappears

With the `database` provider, inline storage is expected. Hosted upload requires the selected provider's credential in `server/credentials.lua`, and only embedded images larger than 100 KiB are offloaded during save or publish.

Missing credentials and failed uploads leave the original embedded data in the design. Enable `Config.Debug` temporarily for upload errors. If an already-hosted image disappears, verify its URL is still accessible; the saved design only retains that URL.

---

## AI Is Unavailable or Fails

| Symptom | Check |
|---|---|
| Addon is not running | Install `gsmd-ai` separately and start it after the main designer |
| API key is not configured | Set the companion's `GoogleApiKey` and restart it |
| In-game capture is disabled | Start `screenshot-basic`; ordinary map snips do not need it |
| Queue is busy | Wait for pending requests or review the companion's queue limits |
| Provider returns no image or an error | Review model access, quota, billing and the server error message |
| Request times out | Check outbound connectivity and `RequestTimeoutMs` |

The companion uses the main designer's access check. Do not share API keys or webhook URLs when requesting support.

---

## Exported Resource Is Missing Features

The export contains baked imagery, not native blips, circle/rectangle zone blips, HUD overrides or postal commands. Cayo Perico extra cells also need a compatible drawer; read the generated export's `README.md`.

If files are not written, check resource-folder write permissions, use a valid export name and inspect both the client and server console. Exporting with an existing name replaces that output folder.

---

## Getting Help

Include the resource version, server artifact version, selected storage backend, exact reproduction steps and relevant console errors. Mention any other resource streaming minimap assets or controlling the radar.

See [Support & Discord](../../support.md).
