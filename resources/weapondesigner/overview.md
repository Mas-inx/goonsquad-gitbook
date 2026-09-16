# GS Weapon Designer

GS Weapon Designer is an in-game weapon skin studio for FiveM. Players pick a weapon, paint a custom texture on its body and attachments with layered editing tools, preview the result on a live 3D model, and forge the design into a real inventory weapon they can carry and show to other players.

These pages document version **0.2.0**.

**Price:** $29.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7644789)

**Open Source:** $49.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7644806)

**Product page:** [goonsquadstudios.com/weapon-designer](https://goonsquadstudios.com/weapon-designer) — full feature breakdown, FAQ and guides

{% embed url="https://www.youtube.com/watch?v=xea1Ny8j3bw" %}

{% hint style="info" %}
Two editions are available: the standard version protected by FiveM Asset Escrow (configuration, credentials, install files, SQL, and weapon templates remain open), and a fully open source version. Both are functionally identical.
{% endhint %}

---

## Highlights

### Design Studio

- Layer-based canvas with drawing, erasing, shapes, text, transforms, image uploads, and HTTPS asset imports
- Colour-coded UV wireframe guide rendered onto the canvas so players can see exactly where each area of the weapon sits
- Live 3D preview with orbit controls, updated as the design changes
- Per-part editing: weapon body, magazines, suppressors, scopes, grips, and flashlights are each designable surfaces
- Undo/redo and draft saving, with full version history per design
- Optional external studio tools, including AI image generation integrations

### Slot System

- Every published design occupies a pre-built weapon slot, so custom weapons behave like real addon weapons — no client mods and no texture conflicts
- Slot counts per weapon and the server-wide design ceiling are controlled by a single JSON file, `data/weapon_slots.json`
- Slots are recycled automatically when a design is deleted
- See [Configuration](configuration.md#the-slot-system-dataweapon_slotsjson) for the full slot system reference

### Access and Monetization

- Optional job and grade gating, enforced on every server action
- Optional in-world designer stations with marker prompts
- Client and server exports for opening full or limited designer sessions
- Configurable Tebex packages with persistent entitlements, online auto-open, and offline claiming
- Separate weapon-forge and AI-generation allowances per package

### Armory and Moderation

- Player armory UI for browsing, loading, equipping, and deleting designs
- Design version history with restore to any previous snapshot
- Optional admin approval queue: designs stay pending and grant no weapon until staff approve them
- Full audit log of grants, approvals, rejections, and deletions

### Runtime Skins

- Designs are painted onto the weapon at runtime and are visible to nearby players
- Equipped designs persist across disconnects, server restarts, and framework load events
- Deleting a published design removes the matching weapon from the owner's inventory and frees its slot
- 29 vanilla weapon templates included, covering rifles, pistols, SMGs, shotguns, and melee weapons

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
| `ox_inventory` | Supported (real weapon items via the bundled bridge module) |
| QBCore inventory | Supported |
| Jaksam Inventory | Supported |
| CodeM Inventory | Supported |
| Quasar Advanced Inventory (`qs-inventory`) | Supported |
| AK47 Inventory (`ak47_inventory`) | Supported |
| ESX inventory | Supported |

With `ox_inventory`, forged designs are granted as real weapon items with working attachments. All other inventories use a `gs_customweapon` ticket item that equips the design on use. See [Installation](installation.md) for the required item definition for each backend.

### Media Providers

Design preview images can be hosted with one of three storage providers:

| Provider | Credential |
|---|---|
| Database | None |
| FiveManage | `FiveManageApiKey` |
| Discord webhook | `DiscordWebhookUrl` |

---

## Requirements

- A modern FiveM server artifact with Node.js 22 support
- `oxmysql`
- QBCore, Qbox, ESX, or standalone
- A supported inventory backend with the item definitions from the `install/` folder
- Optional FiveManage API key or Discord webhook for hosted preview images
- Optional `gswd-ai` companion resource for AI generation

{% hint style="info" %}
The AI tool is provided by the optional `gswd-ai` companion resource. GS Weapon Designer enforces per-pass AI allowances and hosts generated media, but the AI provider key lives server-side in the companion resource and is never exposed to clients.
{% endhint %}

---

## Next Steps

1. Follow [Installation](installation.md).
2. Review [Configuration](configuration.md), especially the slot system.
3. Set up paid designer passes with [Tebex Integration](tebex.md), if needed.
4. Connect custom resources through [API & Exports](exports.md).
