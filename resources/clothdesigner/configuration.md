# Configuration

ClothDesigner has **two** config files:

- `shared/config.lua` — runs on both client and server. Safe to edit, no secrets.
- `server/credentials.lua` — server-only. API keys and webhook URLs live here.

---

## Credentials (`server/credentials.lua`)

```lua
GSCD = GSCD or {}
GSCD.Credentials = GSCD.Credentials or {}

GSCD.Credentials.GoogleApiKey      = ''
GSCD.Credentials.DiscordWebhookUrl = ''
```

| Field | Purpose |
|-------|---------|
| `GoogleApiKey` | Google Gemini API key. Only required if `Config.AI.enabled = true`. Get one at [aistudio.google.com](https://aistudio.google.com/apikey). |
| `DiscordWebhookUrl` | Discord webhook used as a CDN for AI outputs and large user uploads. Required for AI Mode. Required for any user upload above ~12 KB base64 (otherwise FiveM's NUI packet limit kills the upload). |

> Never put these values in `shared/config.lua` — that file is read by clients too.

---

## General

```lua
Config.Debug = true
```
`true` = verbose `[gs-clothdesigner]` server prints + `GSCD.Utils.Debug(...)` client prints. Turn off in production.

```lua
Config.StateBagKey = 'gs_clothdesigner:wearable'
```
Key used on `Player(src).state` to broadcast equipped wearables to all clients. Only change if it conflicts with another resource.

```lua
Config.CallbackEvent         = 'gs-clothdesigner:server:callback'
Config.CallbackResponseEvent = 'gs-clothdesigner:client:callback'
```
Event names used by the internal client/server callback bridge.

```lua
Config.InventoryItemName  = 'gs_customshirt'
Config.InventoryItemLabel = 'Custom T-Shirt'
```
Item the studio prints when a design is published. Must match the entry you added in your inventory items file.

---

## Asset Uploads

```lua
Config.AllowAssetUrlImports = true
Config.AllowFileUploads     = true
Config.AssetUploadProvider  = 'discord'
```

| Field | Description |
|-------|-------------|
| `AllowAssetUrlImports` | Players can paste an HTTPS image URL into the studio's asset library. The server downloads it once and stores it as a referenceable asset. |
| `AllowFileUploads` | Players can drag & drop or pick a file from the asset library. |
| `AssetUploadProvider` | `'discord'` (recommended) hosts uploaded media on a Discord CDN via webhook. Anything else falls back to inline base64 storage in MySQL. |

```lua
Config.MaxUploadBytes  = 4 * 1024 * 1024
Config.MaxPreviewBytes = 4 * 1024 * 1024
```
Maximum size of a single uploaded image, and of a saved design's flat preview PNG. 4 MB is enough for 1024×1024 PNG previews with full-canvas detail.

```lua
Config.SupportedMimeTypes = {
    ['image/png']  = 'png',
    ['image/jpeg'] = 'jpg',
    ['image/webp'] = 'webp',
}
```
Whitelisted MIME types for uploads and URL imports.

---

## Discord Bridge

```lua
Config.Discord = {
    filenamePrefix = 'gscd_media',
}
```
Prefix used when sending uploads to the Discord webhook. Filenames look like `gscd_media_<purpose>_<name>_<timestamp>.<ext>`.

---

## Layer Validation

These hard limits are checked on the server when a design is saved. They protect the database and the wire from absurd payloads.

```lua
Config.LayerValidation = {
    maxLayers              = 64,
    maxJsonBytes           = 2 * 1024 * 1024,
    maxTextLength          = 256,
    maxStrokePoints        = 8192,
    maxEmbeddedImageBytes  = 2 * 1024 * 1024,
    maxDimension           = 4096,
    maxScale               = 10.0,
    maxRotation            = 3600.0,
}
```

| Field | Description |
|-------|-------------|
| `maxLayers` | Total layer count per design. |
| `maxJsonBytes` | Total size of the layer JSON sent to the server. |
| `maxTextLength` | Max characters for a text layer. |
| `maxStrokePoints` | Max points in a single brush / shape stroke. |
| `maxEmbeddedImageBytes` | Max size of an inline base64 image inside a layer. |
| `maxDimension` | Max width or height in pixels for any layer transform. |
| `maxScale` | Cap on a layer's `scale` transform. |
| `maxRotation` | Cap on a layer's `rotation` transform (degrees). |

---

## Runtime (DUI Texture Replacement)

```lua
Config.Runtime = {
    scanInterval         = 750,
    renderDistance       = 45.0,
    losGraceMs           = 2500,
    duiWidth             = 1024,
    duiHeight            = 1024,
    imageRequestCooldown = 2500,
    duiInitialDelayMs    = 400,
    duiRetryIntervalMs   = 450,
    duiRetryWindowMs     = 4500,
}
```

| Field | Description |
|-------|-------------|
| `scanInterval` | Milliseconds between sweeps that look for nearby players with active wearables to bind. |
| `renderDistance` | Maximum distance in metres at which a remote player's DUI texture is allocated. Beyond this the texture is freed to save memory. |
| `losGraceMs` | Grace window after a remote player goes out of line-of-sight before their DUI handle is released. Prevents flicker as players walk behind cover. |
| `duiWidth` / `duiHeight` | Size of the runtime-rendered texture. 1024×1024 matches every shipped silhouette's UV layout. |
| `imageRequestCooldown` | Per-design cooldown on requesting the design preview from the server. Prevents request storms when a player walks past a crowd. |
| `duiInitialDelayMs` | Wait after creating a DUI before binding the texture, so CEF has a frame to render. |
| `duiRetryIntervalMs` | Re-bind retry interval if `AddReplaceTexture` reports the texture isn't ready yet. |
| `duiRetryWindowMs` | Total time spent retrying before giving up on a single bind attempt. |

For most servers, the defaults are correct. Lower `renderDistance` if you're hitting the FiveM DUI handle limit on heavily-populated servers (~32 active DUIs is a safe cap).

---

## Designer Stations & Job Gating

```lua
Config.Designer = {
    jobs = {
        clothingdesigner = 0,
        ambulance        = 0,
    },
    stations = {
        {
            label            = 'Clothing Designer Station',
            coords           = vec3(-1194.96, -767.87, 17.32),
            marker           = vec3(1.4, 1.4, 0.6),
            interactDistance = 1.7,
            drawDistance     = 20.0,
        },
    },
}
```

### `jobs`

Map of `jobName -> minimumGrade`. A player can open the studio at any station if their current job is in this map and their grade is at least the listed value.

To make the studio public, add a catch-all job:

```lua
jobs = {
    unemployed = 0,
}
```

To bypass job gating entirely, call the export from your own job/UI flow:

```lua
exports['gs-clothdesigner']:openDesigner()
```

### `stations`

Array of design stations. Each entry:

| Field | Description |
|-------|-------------|
| `label` | Help text shown above the marker. |
| `coords` | World position of the marker (XYZ). |
| `marker` | Marker scale (X/Y/Z dimensions). |
| `interactDistance` | Distance at which the **E** prompt appears. |
| `drawDistance` | Distance at which the marker is drawn (for performance). |

You can add as many stations as you like — every one of them opens the same studio.

---

## AI Mode

```lua
Config.AI = {
    enabled = true,
    model   = 'gemini-3.1-flash-image-preview',
}
```

| Field | Description |
|-------|-------------|
| `enabled` | Master switch for AI Mode in the studio. Set `false` to hide the AI tool entirely. |
| `model` | Gemini model name. The default is the latest image-capable preview as of release. Any model that supports `IMAGE` `responseModalities` will work. |

> AI Mode also requires `GSCD.Credentials.GoogleApiKey` and `GSCD.Credentials.DiscordWebhookUrl`. With either missing, the studio shows a "AI design generation is disabled / not configured" message instead of generating.

---

## Component Categories

```lua
Config.ComponentCategories = {
    tops        = { component = 11, article = 'shirt',      label = 'Tops'        },
    bottoms     = { component = 4,  article = 'pants',      label = 'Bottoms'     },
    undershirts = { component = 8,  article = 'undershirt', label = 'Undershirts' },
    shoes       = { component = 6,  article = 'shoes',      label = 'Shoes'       },
}
```

Maps `cloth_templates/<gender>/<category>/` directory names to GTA ped component IDs. Category folders that don't appear in this map are skipped during discovery.

The default mapping covers the four most common slots; you can add/remove entries to match your `cloth_templates/` layout.

---

## Supported Player Models

```lua
Config.SupportedPlayerModels = {
    [`mp_m_freemode_01`] = true,
    [`mp_f_freemode_01`] = true,
}
```

Whitelisted ped models. A player whose model isn't in this map can't open the studio and can't equip a custom shirt — the resource is built around MP freemode UVs and won't apply correctly to non-freemode peds.

> The dict is keyed by model **hash** — use the backtick syntax to convert a model name to its joaat at parse time.

---

## Generation Pipeline

```lua
Config.Generation = {
    sourceTemplateManifest = 'data/manifest.json',
    slotCapacity           = 26,
}
```

| Field | Description |
|-------|-------------|
| `sourceTemplateManifest` | Path the JS discovery module writes its silhouette catalog to. The Lua side reads this on boot. Don't change unless you also fork the JS bundle. |
| `slotCapacity` | Number of texture variants per drawable. **Hard-capped at 26 by GTA** (the variant letters `a..z`). Lowering this is allowed for testing but loses storage. |

---

## FiveManage (Optional Asset Provider)

```lua
Config.FiveManage = {
    apiKey              = '',
    base64Endpoint      = 'https://api.fivemanage.com/api/v3/file/base64',
    filenamePrefix      = 'gscd_asset',
    metadataName        = 'GS Cloth Designer Asset',
    metadataDescription = 'Uploaded from gs-clothdesigner',
}
```

> By default ClothDesigner uses `Config.AssetUploadProvider = 'discord'`. The FiveManage block is included for completeness — if you set `AssetUploadProvider = 'fivemanage'` and supply an `apiKey`, uploads will route through FiveManage instead of Discord.
