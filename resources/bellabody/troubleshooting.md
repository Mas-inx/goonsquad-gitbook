# Troubleshooting

## Resource Does Not Start

- Keep the folder name `gs-bellabody` and confirm `fxmanifest.lua` exists directly inside it.
- Start selected integrations before the clinic and restart after changing adapter settings.
- Check the first server console error and the reported bridge/storage selection.
- Use the complete release folder, including all `stream/`, `web/`, `locales/` and inventory assets.

The shipped interface needs no npm install or build on your server.

## Vendor or Interaction Missing

Check `Config.Shops.clinic.coords`, ped model and blip settings. The default location is near Mount Zonah. For a custom interior, choose a valid walkable location. If target interactions fail, set `Config.Target = 'none'` and test the E prompt. Confirm your player model passes `Config.FemaleModels`.

## Item Cannot Be Used or Image Is Missing

Register all six item definitions, copy icons to the selected inventory and restart the full server. When renaming items, update `Config.Items`, definitions, image filenames and translations together. Standalone with ox_inventory needs the four usable item `server.export` definitions described in [Installation](installation.md#usable-items).

The internal inventory uses `/clinic bag` or `/clinicuse <item>`. A shot only starts treatment next to a waiting patient; it is not an ordinary self-use item.

## Purchase Fails

Check the selected cash/bank balance, allowed player model, vendor distance and inventory capacity. The table weighs 25 kg and kit 4 kg in gram-based inventories. New standalone profiles receive $35,000, enough for the table, kit and first Small BBL recipe. Changing starting cash does not reset an existing wallet.

## Crafting Cannot Start

Carry the Portable Crafting Kit and enough ingredients for the selected batch. Small shots need one syringe and one fat extract; big shots need one syringe and two fat extracts. Only one craft can be active per player. Batch sizes are one through five. Output also needs inventory space.

## Cannot Summon a Customer

Place your own operation table, stand within 40 m, wait out the default 20-second summon cooldown and finish the current walk-in. A table owned by another player cannot receive your client. Check the allowed player model and the reported profile-load error if initialization failed.

## Customer Cannot Reach the Table

Place the table on level ground with clear side access. Avoid walls, narrow doorways and steep terrain. The customer may reposition near the player after the arrival timeout; access onto the stretcher still needs space. Test synchronized scene placement on your server's game build and interiors.

## Patient or Procedure Is Rejected

Use the default networked patient setup with OneSync. The patient must have a valid network ID, the expected female freemode model, caller ownership and a position near the session table. The surgeon must remain nearby. A missing, remote or invalid patient is not eligible for payment.

A Small BBL Shot cannot fulfill a Big BBL request. A Big BBL Shot can upgrade a Small BBL request. If the patient waits longer than the configured patience, summon a new client. Stopping with X returns the shot and grants the configured cancellation grace period.

## No Progress Bar During Treatment

This is expected with the included custom surgeon scene. The cinematic scene carries the procedure; X stops it early. Crafting and fallback procedures use progress bars. Disable `Config.OperationTable.surgeon.camera.enabled` to keep the gameplay camera, or set `surgeon = false` to use fallback procedure animations and progress.

## Custom Bed Has Misaligned Animations

The supplied clips are fitted to `gs_stretcher`. For another model, set `anim = false`, `scene = false` and `surgeon = false`, then tune `lieHeight`, `flipPatient` and pose candidates. Keep the five compiled stream files for the default stretcher. Restart and test placement, get-on/get-off and procedure cancellation.

## Player or NPC Body Does Not Change

The resource simulates an NPC clinic career and does not alter player body meshes. `Config.Customer.keepOutfit = true` preserves the NPC's original outfit. Turning it off applies the procedure's configured clothing effect, not a body-mesh transformation.

## Refund Has Not Appeared

Refunds that do not fit are queued on the profile. Free inventory capacity and wait for the server's 20-second retry. Keep storage writable so owed-item state can persist. Do not delete profile data to repair a missing item.

## Progress or Tables Do Not Persist

- JSON: retain `storage/data.json`, ensure `storage/` exists and is writable, and inspect save errors.
- SQL: ensure oxmysql starts first and the database user can create/read/write the two clinic tables.
- Keep the same backend and player identifiers between restarts.
- Back up both profile and placement data before updates.

SQL and JSON do not automatically transfer progress between each other. A failed profile query is treated as an error instead of creating a blank replacement profile.

## Goal, Award or Leaderboard Looks Wrong

Goals reset on UTC day/week boundaries; Monday begins the weekly period. Rewards are claimed once from the hub. Check IDs and translation keys after editing goal/award data. The leaderboard uses lifetime procedure earnings, excludes reward cash and may display its 60-second cache.

## Language Does Not Follow Server Setting

Use `/cliniclang en`, `es`, `de`, `fr` or `pt-BR`. A stored player override takes precedence over `Config.Locale`. Keep placeholders such as `{cash}` and `{xp}` intact in every language. Restart the resource after editing language files.

## Support

Provide the resource version, framework/inventory versions, selected storage backend, first console error and reproduction steps. Share relevant configuration without private webhook URLs. See [Support & Discord](../../support.md).
