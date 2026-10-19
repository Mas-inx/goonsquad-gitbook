# Updating to 0.2.0

This is an update to an existing GS Minimap Designer installation. Keep the resource name `gs-minimapdesigner`. Replace the Lua/JavaScript runtime and compiled UI together; this update adds server-confirmed save/publish responses.

---

## Back Up the Existing Installation

Stop the resource in a maintenance window and back up:

- The complete resource directory, including `data/`, `custom_maps/` and `exports/`.
- All design/thumbnail database tables selected by `Config.StorageTable`.
- `config/config.lua`, `config/server.lua`, `server/credentials.lua` and modified storage/media/postal integrations.
- Optional `gsmd-ai/config.lua` and any companion changes.

Both JSON and MySQL installs use `data/active.json` for the published sheet. JSON installs also need their sheet files, `data/designs_index.json` and `data/thumbs/`. Do not replace these with the starter files from the release.

## Replace and Merge

1. Replace code, `web/build/` and `stream/minimap_main_map.gfx` together.
2. Merge your configuration into the new files; keep the configured command, ACE, storage backend/table and radar/postal settings.
3. Restore server-only identifiers and credentials. The package has an empty identifier list and blank media/AI keys.
4. Keep your entire existing `data/`, custom map packs and completed exports. The release's default sheet/active pointer are fresh-install defaults only.
5. Merge changes in `server/storage.lua`, `server/media.lua` and `client/postals.lua`; these remain editable. Do not replace the new core event/NUI code with old versions.
6. If using AI, replace the optional companion's runtime separately and merge its config/key. Start it after the designer.
7. Start your selected database adapter first, then the resource. Check that the old published name and sheet still load.

Changing storage backends does not migrate the full catalog. Keep the current backend while applying this update unless you also plan and verify a separate migration.

## Changes to Check

- Version remains **0.2.0**, with author **Goonsquad Studios**.
- Debug logging is off; normal editor/postal commands remain.
- The manifest has six explicit Lua-only escrow exceptions. Runtime data/custom YTD maps need no broad ignore entries.
- Saves/publishes report persistence failures; failed saves and edits made during a save remain dirty.
- Rename/duplicate refuses existing destinations and checks persistence before deleting the source.
- Path-, polygon- and heal-only designs now trigger automatic imagery baking.
- Empty legacy Lua tables are normalized in the editor, and browser sample projects are excluded from the production UI.
- Companion upload request ids are scoped per player, unfinished requests are bounded and generation exceptions free the queue slot.

## In-Game Verification

1. Open an old sheet and verify its layers, theme, image links and thumbnails.
2. Save a draft and publish it; confirm another player receives it.
3. Rename/duplicate a test sheet, and confirm an existing destination is protected.
4. Publish a sheet with only a path, polygon or heal patch on vanilla imagery; verify tiles bake.
5. Test postals, HUD placement, radar/pause map, a custom map pack and a stream export.
6. Restart/rejoin and confirm the preserved published pointer loads the expected sheet.
7. If installed, test the companion with a real configured provider and two authorized users.

The bundled renderer's required license/credits are retained. Goonsquad Studios confirmed separate commercial permission during preparation of this update; retain that permission with your distribution records.

See [Configuration](configuration.md), [Usage](usage.md) and [Troubleshooting](troubleshooting.md).
