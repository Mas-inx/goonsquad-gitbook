# Configuration

ClothDesigner has **two** config files:

- `shared/config.lua` — most options live here
- `server/credentials.lua` — server-only. API keys and webhook URLs

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
| `DiscordWebhookUrl` | Discord webhook used for hosting AI outputs and large user uploads. Required for AI Mode. Recommended for any server that allows player image uploads. |

> Never put these values in `shared/config.lua` — that file is read by clients too.

---

## General

```lua
Config.Debug = true
```
Verbose console logs. Turn off in production.

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
| `AllowAssetUrlImports` | Players can paste an HTTPS image URL into the studio's asset library. |
| `AllowFileUploads` | Players can drag & drop or pick a file from the asset library. |
| `AssetUploadProvider` | `'discord'` (recommended) hosts uploaded media on a Discord CDN via webhook. Anything else falls back to in-database storage. |

```lua
Config.MaxUploadBytes  = 4 * 1024 * 1024
Config.MaxPreviewBytes = 4 * 1024 * 1024
```
Maximum size of a single uploaded image and a saved design's preview. 4 MB is a comfortable default.

```lua
Config.SupportedMimeTypes = {
    ['image/png']  = 'png',
    ['image/jpeg'] = 'jpg',
    ['image/webp'] = 'webp',
}
```
Whitelisted MIME types for uploads and URL imports.

---

## Layer Validation

Hard limits applied when a design is saved. They protect the database and the server from oversized payloads.

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
| `maxJsonBytes` | Total size of a single saved design's data. |
| `maxTextLength` | Max characters for a text layer. |
| `maxStrokePoints` | Max points in a single brush / shape stroke. |
| `maxEmbeddedImageBytes` | Max size of an inline image inside a layer. |
| `maxDimension` | Max width or height in pixels for any layer. |
| `maxScale` | Cap on a layer's `scale` transform. |
| `maxRotation` | Cap on a layer's `rotation` transform (degrees). |

The defaults work for the vast majority of servers. Raise the limits if your players want more layers per design.

---

## Performance

```lua
Config.Runtime = {
    scanInterval         = 750,
    renderDistance       = 45.0,
    losGraceMs           = 2500,
    imageRequestCooldown = 2500,
}
```

| Field | Description |
|-------|-------------|
| `scanInterval` | How often (in ms) clients scan for nearby players wearing custom designs. |
| `renderDistance` | Maximum distance in metres at which a remote player's custom texture is rendered. Beyond this it's released to save memory. |
| `losGraceMs` | Grace window (ms) after a remote player goes out of sight before their texture handle is released. Prevents flicker as players walk behind cover. |
| `imageRequestCooldown` | Per-design cooldown (ms) on requesting design previews. Prevents request storms in crowds. |

For most servers, the defaults are correct. Lower `renderDistance` on heavily-populated servers if you see performance issues from too many simultaneous custom textures rendering.

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

To bypass job gating entirely, call the export from your own resource:

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
| `model` | Gemini model name. The default is the latest image-capable preview at release. |

> AI Mode also requires `GSCD.Credentials.GoogleApiKey` and `GSCD.Credentials.DiscordWebhookUrl`. With either missing, the studio shows an "AI design generation is not configured" message instead of generating.

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

Maps `cloth_templates/<gender>/<category>/` directory names to GTA ped component IDs. Add or remove entries to match the categories you want available in the studio.

---

## Supported Player Models

```lua
Config.SupportedPlayerModels = {
    [`mp_m_freemode_01`] = true,
    [`mp_f_freemode_01`] = true,
}
```

Whitelisted ped models. A player whose model isn't in this map can't open the studio and can't equip a custom shirt — the resource is built around MP freemode peds.

> The dict is keyed by model **hash** — use the backtick syntax to convert a model name automatically.
