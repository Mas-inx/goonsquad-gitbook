# Updating to 1.5.0

This is an update to the existing GS Cloth Designer resource. Keep the name `gs-clothdesigner` and keep the existing database, inventory items and generated apparel. Replace the code and compiled UI together.

---

## Back Up Before Replacing Files

Stop the resource during a maintenance window and back up:

- The entire resource folder and every `gs_clothdesigner_*` database table.
- `shared/config.lua`, `shared/tebex.lua`, `server/credentials.lua` and customized locale JSON files.
- Customized framework, notification, station and inventory adapters.
- `cloth_templates/`, `data/manifest.json`, `generated/`, `stream/generated/` and the generated apparel block in `fxmanifest.lua`.

Do not delete design tables, entitlements, active wearables or existing inventory metadata. Do not rename template source keys or generated collections: saved items refer to them.

---

## Replace and Merge

1. Replace the old resource with the new runtime package. Keep the folder name unchanged.
2. Merge your previous configuration into the new files. Keep the new settings and current manifest script order; copying an entire old config or manifest over the update can remove required fields.
3. Restore private credentials into the server-only credentials file. The release intentionally contains blank values.
4. Merge custom templates into `cloth_templates/`. Preserve the shipped templates and any source paths used by saved designs. Restore custom locales and adapter changes by merging them into the new versions.
5. Restore the old `generated/` and `stream/generated/` contents. Copy the old block between `-- [GENERATED APPAREL START]` and `-- [GENERATED APPAREL END]` into the new manifest. Keep the new version, author, scripts, UI files and escrow configuration above it.
6. Start after the database, framework and inventory. Startup creates missing schema fields and reattaches saved template allocations.
7. Let materialization finish. If the console reports new apparel, restart the server once more to register it. Publishing into an already registered free slot does not require a restart.

If there is no old generated block, allow startup to create it and follow the printed restart instructions. Keep the folder writable so the generator can update its manifest and apparel files.

---

## Review the Defaults

| Setting | Shipped value | Effect |
|---|---|---|
| `Designer.AllowEveryone` | `true` | Every supported framework player can use the normal studio. Set false for job/pass restrictions. |
| `Designer.PrintLimit` | `5` | Character quota shared by studio, approval and export prints. Set zero to disable. |
| `Designer.PrintLimitResetHours` | `6` | Shared quota window; usage survives reconnects and resets on resource restart. |
| `Wardrobe.RequireApproval` | `true` | Prints enter review; staff require the configured wardrobe ACE. |
| `AssetUploadProvider` | `discord` | Configure a webhook or choose a different provider. |
| `Locale` | `en` | Nine editable locale catalogs are included. |
| `Debug` | `false` | Enable temporarily when diagnosing an issue. |
| `RenderSystem` / `ExportPacks` | `runtime` / `false` | Production texture pipeline; experimental pack export stays off. |

Runtime image retry, idle caching, browser creation pacing and background promotion settings are documented in [Configuration](configuration.md#runtime-textures).

---

## Limited-Pass Integrations

Client-supplied clothing and AI amounts no longer grant or refill a session. Grant access from trusted server code after validating eligibility:

```lua
local ok, reason = exports['gs-clothdesigner']:summonDesigner(source, 2, 4)
```

`summonDesignerForPlayer` is an alias. Existing client exports can open the granted session, but cannot choose its limits. A server grant replaces an existing session, so do not repeat the grant on every menu open. Persistent purchases should use the Tebex entitlement flow, which restores the remaining uses from the database.

See [API & Exports](exports.md) and [Tebex Integration](tebex.md).

---

## Verify After Updating

1. Confirm startup has no database, missing-item, template or apparel errors.
2. Open a saved design and confirm its template and preview still load.
3. Test an existing printed item, equip, take off and reconnect with both component and prop clothing.
4. Upload and import an image using your selected provider.
5. Save a draft, submit a print, approve/reject it with staff, and check the owner's wardrobe and inventory.
6. Confirm the print quota and your public/job access policy.
7. Test a real server-granted limited session and an existing Tebex entitlement; reopening must preserve remaining uses.
8. Confirm locale text, nearby-player texture visibility and console stability on the processed portal build.

The removed development commands are not part of the update. The remaining administrative deletion, rescan and rebuild commands are ACE-restricted. See [Usage](usage.md#commands).

## Runtime Confirmation in This Build

The refreshed 1.5.0 build checks texture readiness and native component/prop readback before confirming an equip. Lost confirmation, a character change during loading, invalid collections and database failures return failure and restore the prior confirmed appearance. Nearby-player texture refresh avoids stripping or locally redressing their peds. Keep the updated client, server and DUI page together.

The optional `gscd-ai` companion has its own editable `config.lua` escrow exception and ships separately with blank credentials. Its existing version is preserved.
