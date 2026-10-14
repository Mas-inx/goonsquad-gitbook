# Installation

## Requirements

- A FiveM GTA V server with OneSync state awareness and a server build supporting Node 22
- `qbx_core`, `oxmysql`, `ox_lib` and `ox_target`
- A configured database connection with permission to create/alter the designs table
- Female/male freemode characters; female is enabled by default
- The Fable nail salon map for the default measured stations, or relocated configuration and matching furniture
- Optional `qbx_management` for the manager's boss menu

## Install the Resource

1. Extract the delivered `gs_nails` folder into your resources directory. Keep its name unchanged.
2. Retain `stream/`, `generated/nail_catalog.json`, `generated/shapes/`, both apparel metadata files and `web/dist/`.
3. Configure your salon in `config.lua`. Verify station targets, hand cameras and both management points against the installed interior.
4. Start dependencies and the interior before the resource:

```cfg
set onesync on
ensure oxmysql
ensure ox_lib
ensure qbx_core
ensure ox_target
# Ensure your nail salon map here.
# Optional: ensure qbx_management
ensure gs_nails
```

The delivered browser assets are already built. Customers do not need Node/npm on their host outside FiveM's bundled server runtime. The packs are prebuilt, so there is no first-start generator or mandatory two-start generation process.

## Database

The resource creates `gs_nails_designs` automatically. It stores ownership, scope, shape, gender, editor layers, image/host reference, thumbnail, deletion status and creation time. It also adds `image_ref` if an older table lacks it. No manual SQL import is required.

Keep this table and your character metadata in backups. Worn nails use `Config.MetadataKey` (default `gs_nails`) in Qbox character metadata. Do not change that key after launch without migrating existing values.

## Job

`Config.AutoRegisterJob = true` registers `Config.JobName` using `Config.JobDefinition` at resource start. Grades 0–3 are Trainee, Technician, Senior Tech and Manager, with configured wages of 50/75/100/125. Manager has `isboss` and `bankAuth` enabled.

Assign the job with your normal admin tools. A trusted server integration can use:

```lua
exports.qbx_core:SetJob(playerSource, 'nailsalon', 0)
```

To maintain the job yourself in Qbox's persistent/shared job definitions, set `Config.AutoRegisterJob = false` and register the same name/grades there. Runtime job creation is repeated on resource start.

## First Salon Check

With two freemode characters, assign one the nail salon job. Seat the customer and technician at the same station, preview a preset, charge a small amount and accept payment. Then test decline, custom-design save/preview/apply, reconnect persistence and both gestures.

Enable the male pack explicitly before testing male customers. Verify that the intended interior's seat/brush/camera alignment is correct; desk-only checks cannot prove native game alignment.

## Asset Escrow

The editable Lua files are `config.lua`, `client/bridge.lua`, `server/bridge.lua` and `server/credentials.lua`. Raw nail models, the asset generator, web source and development tools are absent from the release. Finished stream files are used directly and are not parsed by a runtime generator.

For the seller, upload the prepared ZIP and wait for Cfx processing before distributing the portal-delivered asset. Lua and supported models are processed by Asset Escrow; NUI and JavaScript are outside its supported encryption formats. See the [official Cfx Asset Escrow guide](https://docs.fivem.net/docs/server-manual/asset-escrow/).
