# GS Cloth Designer

GS Cloth Designer is an in-game custom clothing studio for FiveM. Players can choose a garment or prop template, build a texture with layered editing tools, preview it on a 3D freemode ped, and print the result as wearable inventory clothing.

These pages document the **1.3.0 Release Candidate**.

**Price:** $55.00 | [Buy on Tebex](https://goonsquad-inc.tebex.io/)

{% embed url="https://www.youtube.com/watch?v=Y5hUNsV0j3o" %}

---

## Highlights

### Design Studio

- Layer-based canvas with drawing, shapes, text, transforms, effects, undo/redo, image uploads, and HTTPS asset imports
- Live 3D preview using the selected GTA clothing template
- Black or light-gray preview background, saved locally for the next session
- Male and female freemode libraries with clothing components and ped props
- Lazy-loaded library previews and managed WebGL contexts to reduce UI load and context-loss warnings
- Optional external studio tools, including AI image generation integrations

### Access and Monetization

- Job and grade access at configurable world stations
- Client and server exports for opening a limited designer session
- Separate clothing-print and AI-generation allowances
- Configurable Tebex packages with persistent entitlements, online auto-open, and offline claiming
- Configurable rules for consuming clothing allowance on print or save

### Wardrobe and Moderation

- Player wardrobe UI for previewing, wearing, and removing created clothing
- Inventory items toggle the matching clothing on first use and off on second use
- Inventory and wardrobe actions share the same equipped state
- Optional admin approval queue before a design is published or printed
- Approved clothing remains available in the creator's wardrobe even if an inventory item could not be delivered while they were offline

### Runtime Wearables

- Runtime textures are visible to nearby players without rebuilding an outfit
- Equipped clothing persists across disconnects, server restarts, and supported framework load events
- Previous component and prop appearance is restored when custom clothing is removed
- Supports tops, bottoms, undershirts, shoes, masks, arms, bags, accessories, armor, decals, hats, glasses, earwear, watches, and bracelets

---

## Compatibility

### Frameworks

| Framework | Support |
|---|---|
| QBCore | Supported |
| Qbox (`qbx_core`) | Supported |
| ESX Legacy | Supported |

### Inventories

| Inventory | Support |
|---|---|
| QBCore inventory | Supported |
| `ox_inventory` | Supported |
| Jaksam Inventory | Supported |
| CodeM Inventory | Supported |
| Quasar Advanced Inventory (`qs-inventory`) | Supported |
| AK47 Inventory (`ak47_inventory`) | Supported |
| ESX inventory | Supported |

See [Installation](installation.md) for the required item definition for each backend.

### Media Providers

Uploaded assets and saved previews can use one of three storage providers:

| Provider | Credential |
|---|---|
| Database | None |
| FiveManage | `FiveManageApiKey` |
| Discord webhook | `DiscordWebhookUrl` |

---

## Requirements

- A modern FiveM server artifact with Node.js 22 support
- `oxmysql`
- QBCore, Qbox, or ESX
- A supported inventory backend with the custom clothing item installed
- MP male or female freemode player models
- Optional FiveManage API key or Discord webhook for hosted media
- Optional compatible studio-tool resource for AI generation

{% hint style="info" %}
The AI tool is registered by a companion or third-party studio-tool resource. GS Cloth Designer enforces limited-session AI allowances and hosts generated media, but it does not contain a Gemini API key or AI model setting.
{% endhint %}

---

## Next Steps

1. Follow [Installation](installation.md).
2. Review [Configuration](configuration.md).
3. Set up paid passes with [Tebex Integration](tebex.md), if needed.
4. Connect custom resources through [API & Exports](exports.md).
