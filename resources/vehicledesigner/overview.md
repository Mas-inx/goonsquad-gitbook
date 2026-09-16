# GS Vehicle Designer

GS Vehicle Designer is an in-game vehicle livery studio for FiveM. Players pick a vehicle from the server's pool, paint a custom finish directly onto a live 3D model or on a flat UV canvas, and print the result as an inventory item that fits the livery to a real vehicle.

These pages document version **0.1.0**.

**Price:** $55.00 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7654694)

**Product page:** [goonsquadstudios.com/vehicle-designer](https://goonsquadstudios.com/vehicle-designer) — full feature breakdown, FAQ and guides

{% embed url="https://www.youtube.com/watch?v=S4I-TqTnBxc" %}

{% hint style="info" %}
The manifest is configured for FiveM Asset Escrow. Configuration, credentials, install snippets, and framework, inventory, owned-vehicle, and notification bridges stay editable. Vehicle integration assets stay readable for conversion, previews, and exports. JavaScript and NUI files are outside Cfx's currently supported escrow formats. See [Configuration](configuration.md#file-layout).
{% endhint %}

---

## Highlights

### Design Studio

- 1024x1024 layer-based canvas with brush, eraser, fill, pen, clone, and smudge tools
- Editable text, vector shapes, gradients, layer effects, transforms, and full undo/redo history
- **3D Model** mode paints directly onto the vehicle: brush input raycasts the model and writes ordinary editable stroke layers through the generated UV channel
- **UV Layout** mode exposes the flat texture for precise layer placement and the complete 2D toolset
- Live Three.js preview rendered from the vehicle's own game files, with no spawned entity and no game camera
- Reusable per-player image asset library, custom font loading, and finishing filters
- Local file uploads and HTTP/HTTPS image imports with server-side hostname checks
- Optional studio-tool contract for AI or other external image generators

### Vehicle Pool

- Drop car packs into `vehicle_templates/` and every model is converted and registered automatically on boot
- Whole car packs, single vehicle resources, and per-model folders are all accepted and can be mixed
- Each vehicle is content-signed, so only new or changed templates are reconverted and later restarts are instant
- Tuning-part YFTs, wheel YDRs, animation YCDs, and shared assets are carried along so packs keep their mods and wheels
- Every paint surface is projected into one fixed semantic layout, so a design's placement is consistent across every vehicle
- 16 live finish slots per model, with automatic **series clone** expansion when a vehicle fills up

### Access and Monetization

- ACE, job, and grade checks for studio access, with ownership checks for saved designs
- Optional in-world designer stations with marker prompts and proximity gating
- Configurable Tebex packages with persistent entitlements, online auto-open, and offline claiming
- Separate livery-print and AI-generation allowances per package
- Optional admin approval queue before a finish can be published

### Liveries and Persistence

- Printed items are non-stackable and carry design, version, target model, and display metadata
- Printing captures a render of the actual vehicle wearing the finish and uses it as the inventory item image
- Printed items retain their saved design version; use separate designs for different looks that must appear concurrently
- Owned-vehicle livery storage, startup restoration, and a bundled Qbox garage respawn hook
- Distance-aware runtime textures and replicated state bags show finishes to nearby players
- Unlimited stored draft designs, with immutable version history and restore

---

## Compatibility

### Frameworks

| Framework | Support |
|---|---|
| QBCore | Supported |
| Qbox (`qbx_core`) | Supported |
| ESX Legacy | Supported |
| Standalone | Supported (license identifier fallback) |

The framework is detected automatically. No framework files need to be edited.

### Inventories

| Inventory | Support |
|---|---|
| `ox_inventory` | Supported (client export flow) |
| QBCore inventory | Supported |
| Jaksam Inventory | Supported |
| CodeM Inventory | Supported |
| Quasar Advanced Inventory (`qs-inventory`) | Supported |
| AK47 Inventory (`ak47_inventory`) | Supported |
| ESX inventory | Bridge included; per-item metadata must be preserved by the inventory |

`ox_inventory` uses the client export flow declared in its item definition. Every other backend registers a server-side usable handler that bounces to the client to locate the target vehicle. See [Installation](installation.md#3-inventory-setup) for the required item definition for each backend.

When several backends are running, detection checks Jaksam, CodeM, Quasar, AK47, then `ox_inventory`, followed by framework-native items. Use the open inventory bridge for custom metadata handling.

### Media Providers

Uploaded graphics and printed item renders can use one of three storage providers:

| Provider | Credential |
|---|---|
| Database | None |
| FiveManage | `FiveManageApiKey` |
| Discord webhook | `DiscordWebhookUrl` |

A hosted provider is required for real vehicle renders to appear as inventory item images. See [Configuration](configuration.md#uploads-and-media).

---

## Requirements

- A modern FiveM server artifact with Node.js 22 support
- `oxmysql`
- QBCore, Qbox, ESX, or standalone
- A supported inventory backend with the `gs_vehicle_livery` item installed
- At least one vehicle placed in `vehicle_templates/`
- Optional FiveManage API key or Discord webhook for hosted media and item images
- Optional `gsvd-ai` companion resource for AI generation

{% hint style="info" %}
The AI tool is provided by the optional `gsvd-ai` companion resource. GS Vehicle Designer enforces per-pass AI allowances and hosts generated media, but it contains no AI provider key, generation code, or model setting.
{% endhint %}

---

## Next Steps

1. Follow [Installation](installation.md).
2. Add your cars with [Adding Vehicles](vehicles.md).
3. Review [Configuration](configuration.md), especially access and media.
4. Set up paid designer passes with [Tebex Integration](tebex.md), if needed.
5. Connect custom resources through [API & Exports](exports.md).
