# Integrations

## Editable Files

| File | Purpose |
|---|---|
| `config.lua` | Job, locations, appearance events, limits, media options, locale and UI title |
| `shared/bridge.lua` | Framework detection, boss-menu resource selection and grade helpers |
| `client/bridge.lua` | Player data, duty toggle and boss-menu entry |
| `server/bridge.lua` | Player lookup, cash/bank removal/addition, character metadata and job registration |
| `server/credentials.lua` | Server-only image-host credentials |

These five explicit Lua files are the complete escrow exceptions. Qbox, QBCore and ESX Legacy have built-in bridges. Keep the normalized character identity, job, money, metadata and lifecycle contracts when adapting them. Changing frameworks can change character identifiers; migrate SQL design ownership and worn metadata explicitly rather than expecting automatic conversion.

## Economy and Character Metadata

The server validates selection, job, distance, charge bounds, customer identity and a one-use payment token. Charges reject nonfinite values. Failed technician payouts/metadata saves attempt to refund the customer, and exhausted custom slots prevent application before money is removed.

Bridge money methods must return true on success and false on failure. Metadata methods must return false on failure, including failed clearing during rollback. Current ESX metadata support needs `getMeta`, `setMeta` and `clearMeta`; older builds without metadata use per-character resource KVP. A custom partial metadata API needs a coherent get/set/clear implementation. Keep money changes synchronous when adapting these methods; coordinate persistence and refunds with your economy system.

Worn metadata values are preset ids such as `001_a` or `custom:12`. The authoritative server publishes a descriptor under the configured player-state key; custom leases are published in global state. Designs and editor documents live in SQL. Back up SQL, framework character metadata and server resource KVP when used by older ESX builds.

## Appearance and Clothing

The provided `Config.AppearanceEvents` includes `illenium-appearance:client:reloadSkin`, `qb-clothing:client:loadPlayerClothing` and `skinchanger:modelLoaded`. Add the appropriate local event for another appearance system. The client waits briefly, captures the underlying decal and reapplies saved nails.

The nails use addon collections `mp_f_gs_nails` and `mp_m_gs_nails` on component 10. Do not let another resource continuously replace that component. Consider [API & Exports](exports.md) when another activity needs to block chair exit or stop a gesture.

## Target, Management and Interior

`ox_target` is required for chairs, kiosks, duty and management points. `ox_lib` handles prompts, notifications, keybinds and callbacks. The optional boss-menu resource is `qbx_management` for Qbox, `qb-management` for QBCore, or `esx_society` for ESX. Start it and its dependencies before GS Nails, or disable the boss target. See [Installation](installation.md) for ESX job and society setup.

The salon map is separate. The default station positions were built for the Silhouette nail salon interior. Manicure clips, gesture clips and brush prop are included in `stream/anim/`; the salon building/furniture map is not. Measure new seats, origins, target points and cameras when relocating.

## Hosted Images

Database storage is the default and needs no host account. To enable another provider:

```lua
-- config.lua
Config.Media.provider = 'fivemanage' -- or 'discord'

-- server/credentials.lua
Credentials = {
    FiveManageApiKey = 'YOUR_API_KEY',
    DiscordWebhookUrl = '',
}
```

For Discord, set the provider to `discord`, leave the API key empty and put your webhook URL in `DiscordWebhookUrl`. Never put credentials in shared configuration. The shipped credential fields are blank.

The finished image and imported layer pictures can be hosted; small thumbnails remain in SQL. Failed uploads stay in database storage. Existing inline designs can migrate to the chosen provider when used. Discord uploads store the webhook message id so signed attachment links can be refreshed; keep the same webhook available for those messages. Changing providers does not erase old designs, but deleting an old host/webhook can make its hosted files unavailable.

The server downloads hosted images and gives clients inline copies where possible. It caches recent results. Lua-to-JavaScript uploads are chunked and queued; `uploadConcurrency` defaults to 2. FiveManage endpoint/prefix/path and Discord filename prefix/username are configurable.

## Import from a Link

`allowUrlImport = true` allows an active studio/technician to request a PNG, JPEG or WebP download. `maxImportBytes` defaults to 4 MiB with a short per-player cooldown. The server validates addresses and redirects, pins the validated address for the connection and rejects nonpublic IPv4/IPv6 destinations. URL imports do not allow arbitrary server file access. Set this option to false to disable importing links while retaining local image import.

## Source Asset Updates

The customer package uses finished assets. Adding new raw models/shapes requires a new developer build from the maintained source, followed by rebuilding the catalog/templates and re-uploading the resulting release. Do not run an asset generator over portal-encrypted models or remove the shipped catalog/template directories.
