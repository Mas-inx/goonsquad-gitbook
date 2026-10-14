# Configuration

GS Cloth Designer uses three configuration files:

| File | Purpose | Client-readable |
|---|---|---|
| `shared/config.lua` | Main resource, media, runtime, station, wardrobe, and category settings | Yes |
| `shared/tebex.lua` | Tebex packages and limited-pass usage rules | Yes |
| `server/credentials.lua` | FiveManage token and Discord webhook | No |

{% hint style="warning" %}
Keep API tokens and webhook URLs in `server/credentials.lua`. Never move secrets into either shared file.
{% endhint %}

---

## General

```lua
Debug = false
Locale = 'en'
InventoryItemName = 'gs_customshirt'
InventoryItemLabel = 'Custom Clothing'
```

| Setting | Description |
|---|---|
| `Locale` | Selects the matching editable JSON catalog under `locales/`; missing or invalid catalogs fall back to English. |
| `Debug` | Adds detailed client and server diagnostics. Use it while installing or investigating an issue; disable it for normal production use. |
| `InventoryItemName` | Item added when clothing is printed. It must match the active inventory definition. |
| `InventoryItemLabel` | Fallback display label used by integrations and notifications. |

`StateBagKey`, `CallbackEvent`, and `CallbackResponseEvent` are protocol settings. Change them only when another resource conflicts and every integration is updated to match.

Available locale codes are `en`, `es`, `fr`, `pt-br`, `de`, `it`, `nl`, `pl` and `tr`. Restart the resource after changing `Locale` or a catalog. Keep the English message keys and formatting placeholders unchanged when translating; missing entries fall back to English. No UI rebuild is needed.

---

## Rendering and Pack Export

```lua
RenderSystem = 'runtime'
ExportPacks = false
```

`runtime` is the recommended render system. It applies saved textures through managed DUI runtime textures and restores them after supported player-load events.

`hybrid` is experimental and may cause crashes. Do not enable it on a live server unless Goonsquad support specifically asks you to test it.

`ExportPacks` creates standalone resources under `output/` while printing. It is intended for development and should remain `false` on production servers.

---

## Uploads and Imports

```lua
AllowAssetUrlImports = true
AllowFileUploads = true
AssetUploadProvider = 'discord'
MaxUploadBytes = 4 * 1024 * 1024
MaxPreviewBytes = 4 * 1024 * 1024
```

| Setting | Description |
|---|---|
| `AllowAssetUrlImports` | Allows players to import public HTTP or HTTPS image URLs. |
| `AllowFileUploads` | Allows local image selection and drag-and-drop uploads. |
| `AssetUploadProvider` | `database`, `fivemanage`, or `discord`. |
| `MaxUploadBytes` | Maximum uploaded asset size. The UI checks this before `FileReader` reads the file, and the server validates it again. |
| `MaxPreviewBytes` | Maximum generated design-preview or studio-tool reference size. |

Accepted MIME types are configured separately:

```lua
SupportedMimeTypes = {
    ['image/png'] = 'png',
    ['image/jpeg'] = 'jpg',
    ['image/webp'] = 'webp',
}
```

Players receive an immediate error when a selected file exceeds the client-side limit. The server still rejects oversized, invalid, or unsupported payloads so a modified NUI cannot bypass the limit.

### Upload Queue

```lua
Queues = {
    Upload = {
        Enabled = true,
        MaxConcurrent = 1,
        MaxPending = 12,
        BusyMessage = 'The media upload queue is busy. Please try again in a moment.',
    },
}
```

The upload queue covers asset uploads, URL imports, design previews, studio-tool reference images, and generated-media hosting. It prevents many simultaneous HTTP and base64 operations from hitting the server or provider at once.

| Setting | Description |
|---|---|
| `Enabled` | Set `false` to execute media jobs immediately. Keeping it enabled is recommended. |
| `MaxConcurrent` | Media jobs allowed to run at the same time. Start at `1`; increase only after measuring your host and provider. |
| `MaxPending` | Waiting jobs accepted before new requests receive `BusyMessage`. `0` allows no waiting jobs. |
| `BusyMessage` | Error shown when the pending queue is full. |

### FiveManage

```lua
FiveManage = {
    base64Endpoint = 'https://api.fivemanage.com/api/v3/file/base64',
    filenamePrefix = 'gscd_asset',
    metadataName = 'GS Cloth Designer Asset',
    metadataDescription = 'Uploaded from gs-clothdesigner',
    path = 'gs-clothdesigner',
    retentionExempt = false,
}
```

Set `AssetUploadProvider = 'fivemanage'` and place the token in `server/credentials.lua`:

```lua
GSCD.Credentials.FiveManageApiKey = 'YOUR_API_KEY'
```

The resource sends a base64 data URL to the endpoint. Keep the default endpoint unless FiveManage documents a replacement. `path` is the optional folder in the FiveManage team, while `retentionExempt` should only be enabled when the token and account permit it.

### Discord

```lua
Discord = {
    filenamePrefix = 'gscd_media',
}
```

Set `AssetUploadProvider = 'discord'` and configure:

```lua
GSCD.Credentials.DiscordWebhookUrl = 'YOUR_DISCORD_WEBHOOK_URL'
```

### Database

Set `AssetUploadProvider = 'database'`. Assets and previews are stored as data in MySQL, so monitor database size on an active server.

---

## Image Optimization

```lua
ImageOptimization = {
    enabled = true,
    maxDimension = 1024,
    quality = 0.9,
    skipBelowBytes = 200 * 1024,
    textureFormat = 'webp',
    textureQuality = 0.92,
}
```

The studio resizes large uploads while preserving their proportions; small images below `skipBelowBytes` are left untouched. `quality` controls upload encoding. Printed runtime textures use WebP by default; `textureQuality` controls their compression. PNG is used when hybrid rendering or standalone pack export requires baking. These settings reduce transfer and image memory costs; the server's separate payload and layer limits still apply.

---

## Large-Payload Transport

```lua
Network = {
    callbackTimeoutMs = 30000,
    chunkBytes = 32768,
    uploadBytesPerSecond = 65536,
    uploadBurstBytes = 196608,
    chunkAckTimeoutMs = 15000,
    uploadResponseTimeoutMs = 180000,
    transferIdleTimeoutMs = 30000,
}
```

Large layer documents and images use acknowledged chunks. `chunkBytes` is capped at 61,440 bytes; upload pace and burst settings are per player. The callback timeout covers ordinary calls, while the longer upload-response timeout lets a completed upload finish processing. Missing acknowledgements and idle transfers time out and release buffered data.

Keep the default upload budget unless your server's event limits and other resources have been checked together. Raising the budget can make uploads faster but can also trigger connection rate limits. Optional `Network.maxTransferBytes` sets an explicit total payload ceiling; otherwise it is derived from the configured image and layer limits.

---

## Layer Validation

```lua
LayerValidation = {
    maxLayers = 64,
    maxJsonBytes = 2 * 1024 * 1024,
    maxTextLength = 256,
    maxStrokePoints = 8192,
    maxEmbeddedImageBytes = 2 * 1024 * 1024,
    maxDimension = 4096,
    maxScale = 10.0,
    maxRotation = 3600.0,
}
```

These server-side limits protect save operations and the database from excessively large designs.

| Setting | Description |
|---|---|
| `maxLayers` | Total layers allowed in one design. |
| `maxJsonBytes` | Maximum serialized layer JSON size. |
| `maxTextLength` | Characters allowed in one text layer. |
| `maxStrokePoints` | Stored points allowed in one freehand stroke. |
| `maxEmbeddedImageBytes` | Maximum image data embedded in a layer. |
| `maxDimension` | Maximum accepted width or height for uploaded or generated images. |
| `maxScale` | Maximum layer scale multiplier. |
| `maxRotation` | Maximum absolute rotation value in degrees. |

---

## Runtime Textures

```lua
Runtime = {
    scanInterval = 750,
    renderDistance = 100.0,
    prepareDistance = 200.0,
    losGraceMs = 30000,
    preApplyTimeoutMs = 3000,
    postApplyHardReloadComponents = { [4] = true },
    postApplyHardReloadFallbackMs = 650,
    duiWidth = 1024,
    duiHeight = 1024,
    imageRequestCooldown = 2500,
    imageRetryMaxMs = 30000,
    imageFetchTimeoutMs = 10000,
    imageCacheIdleMs = 5 * 60 * 1000,
    duiInitialDelayMs = 450,
    duiRetryIntervalMs = 500,
    duiRetryWindowMs = 2500,
    duiCreateIntervalMs = 100,
    duiReadyTimeoutMs = 5000,
    backgroundPromotion = true,
    promotionStartDelayMs = 5000,
    promotionChunkRows = 8,
    promotionChunkIntervalMs = 1000,
    promotionTimeoutMs = 10 * 60 * 1000,
}
```

| Setting | Description |
|---|---|
| `scanInterval` | Milliseconds between nearby-player wearable scans. |
| `renderDistance` | Fallback distance used when `prepareDistance` is absent. |
| `prepareDistance` | Maximum distance at which a client prepares another player's custom textures. |
| `losGraceMs` | How long a prepared texture is retained after range or line of sight is lost. |
| `preApplyTimeoutMs` | Maximum preparation wait before the clothing variation is applied. |
| `postApplyHardReloadComponents` | Components allowed one delayed hard reload when GTA keeps a stale local material cache. Component `4` is pants. |
| `postApplyHardReloadFallbackMs` | Delay before that one-shot fallback. |
| `duiWidth`, `duiHeight` | Hidden runtime texture canvas resolution. |
| `imageRequestCooldown` | Per-design render-image request cooldown. |
| `duiInitialDelayMs` | Initial wait before binding a new DUI texture. |
| `duiRetryIntervalMs` | Delay between binding retries. |
| `duiRetryWindowMs` | Total retry window. |
| `imageRetryMaxMs` | Ceiling for exponential backoff after failed image requests. |
| `imageFetchTimeoutMs` | Background image-fetch timeout. Local equip preparation still observes `preApplyTimeoutMs`. |
| `imageCacheIdleMs` | Keeps unused downloaded images for five minutes by default; `0` releases them immediately. |
| `duiCreateIntervalMs` | Spaces browser-surface creation during bursts. Local-player work receives priority. |
| `duiReadyTimeoutMs` | Time allowed for the browser canvas to acknowledge drawing its image. |
| `backgroundPromotion` | Gradually moves an immediately visible DUI image into a pooled runtime texture. |
| `promotionStartDelayMs` | Initial delay before background texture promotion. |
| `promotionChunkRows`, `promotionChunkIntervalMs` | Rows per acknowledged transfer and delay between transfers. Defaults spread a 1024-pixel texture across roughly two minutes. |
| `promotionTimeoutMs` | Promotion deadline, including queue time. |

The 1.5.0 UI automatically virtualizes library previews and reuses or disposes WebGL renderers. There is no separate switch for those optimizations.

{% hint style="info" %}
For a high-population server, reduce `prepareDistance` before making scans more frequent. Raising DUI resolution, distance, or queue concurrency increases resource use.
{% endhint %}

---

## Designer Access, Print Quota and Stations

```lua
Designer = {
    AllowEveryone = true,
    PrintLimit = 5,
    PrintLimitResetHours = 6,
    jobs = {
        clothingdesigner = 0,
        ambulance = 0,
    },
    stations = {
        {
            label = 'Clothing Designer Station',
            coords = vec3(-1194.96, -767.87, 17.32),
            marker = vec3(1.4, 1.4, 0.6),
            interactDistance = 1.7,
            drawDistance = 20.0,
        },
    },
}
```

`AllowEveryone = true` allows every supported framework player to use the normal studio. Set it to `false` to require the configured `jobs` and minimum grades, or an authorized limited pass. Add any number of stations.

`PrintLimit` defaults to five successful prints per character in a shared six-hour window; set `0` to disable this quota. `PrintLimitResetHours` changes the interval. Reprints and the `printShirtItem` server export count; drafts and approval submissions do not. Approval charges the creator, even while offline, and refuses approval when their window is full. The quota also applies to job users and paid passes. It survives reconnects, but is held in memory and resets when the resource restarts. Failed item delivery outside approval refunds the reserved print.

For a paid-pass-only setup, set `AllowEveryone = false` and remove free jobs from `jobs`. Pass allowances and the print quota are separate limits.

---

## Wardrobe and Approval

```lua
Wardrobe = {
    Enabled = true,
    Command = 'gscd_wardrobe',
    AdminCommand = 'gscd_clothing_review',
    AdminAce = 'gscd.clothdesigner.admin',
    RequireApproval = true,
}
```

| Setting | Description |
|---|---|
| `Enabled` | Enables the player wardrobe and admin review UI. |
| `Command` | Player command for created clothing. Use an empty string to avoid registering it. |
| `AdminCommand` | Command that opens pending designs for review. |
| `AdminAce` | ACE checked by the server before admin data or actions are returned. |
| `RequireApproval` | Sends print requests to the review queue instead of immediately publishing and granting the item. |

```cfg
add_ace group.admin gscd.clothdesigner.admin allow
```

---

## Clothing and Prop Categories

Component folders map to GTA component IDs:

| Folder | ID | Label |
|---|---:|---|
| `masks` | 1 | Masks |
| `arms` | 3 | Arms |
| `bottoms` | 4 | Bottoms |
| `bags` | 5 | Bags |
| `shoes` | 6 | Shoes |
| `accessories` | 7 | Accessories |
| `undershirts` | 8 | Undershirts |
| `armor` | 9 | Armor |
| `decals` | 10 | Decals |
| `tops` | 11 | Tops |

Prop folders use GTA prop IDs:

| Folder | ID | Label |
|---|---:|---|
| `hats` | 0 | Hats |
| `glasses` | 1 | Glasses |
| `ears` | 2 | Earwear |
| `watches` | 6 | Watches |
| `bracelets` | 7 | Bracelets |

Component `0` and component `2` are intentionally excluded. Only add a category when the matching templates follow the expected GTA naming and texture structure.

---

## Supported Peds

```lua
SupportedPlayerModels = {
    [`mp_m_freemode_01`] = true,
    [`mp_f_freemode_01`] = true,
}
```

Custom clothing is designed for the GTA Online male and female freemode peds. Other models cannot open or wear designs unless the resource and all templates are adapted for them.

---

## Tebex Configuration

Package allowances and claiming rules live in `shared/tebex.lua`, not the main config. See [Tebex Integration](tebex.md).

---

## AI and Studio Tools

There is no `Config.AI` or Google API key in GS Cloth Designer 1.5.0. AI appears when another resource registers a studio tool through `registerStudioTool`. That integration should consume or refund an AI allowance through the server exports documented in [API & Exports](exports.md#studio-tool-and-ai-integration).
