# Goonsquad ClothDesigner

An in-game custom clothing studio for FiveM. Players walk up to a designer station, open a full-screen design canvas, paint, draw, type, upload, or **AI-generate** a graphic onto a real GTA garment, then save and print it as a wearable inventory item that anyone on the server can equip and see. Supports **QBCore**, **Qbox**, and **ESX** out of the box.

**Price:** $55.00 | [Buy on Tebex](https://goonsquad-inc.tebex.io/)

{% embed url="https://www.youtube.com/watch?v=Y5hUNsV0j3o" %}

---

## Features

### Design Studio

- Full-screen, in-game design canvas with layers, transforms, undo/redo, fonts, shapes, and image uploads
- Live 3D preview of the design wrapped onto the actual GTA garment as you edit
- Library of every available silhouette, filterable by gender and category
- "My Designs" panel showing every design the player has saved, with thumbnails
- Drag-and-drop image upload (PNG / JPEG / WebP, up to 4 MB by default)
- Asset URL import — paste any public HTTPS image URL
- AI Mode — type a prompt and get a Gemini-generated design back as a new layer

### Wearables

- Equipping a printed shirt swaps the player's shirt / pants / undershirt / shoes to the saved design — no character editor required
- All other players see the equipped design instantly, without a character refresh
- Equipped wearables persist across player disconnects, server restarts, and ped model swaps
- Players can hand printed shirts to friends, sell them, or store them like any other item

### Unlimited Designs

- Server hosts an effectively unlimited number of published designs
- Just drop new garment templates in and run a single command — the resource handles the rest
- No server restart required to publish or wear a new design

### Framework Integration

- Auto-detects QBCore, Qbox (qbx_core + ox_inventory), or ESX
- Uses `ox_inventory` for shirt items when available; falls back to QBCore / ESX inventory otherwise
- Designer-station access is gated by job + grade (configurable per job)
- Printed shirts show their own design preview as the inventory thumbnail (with `ox_inventory`)

### Built-in Hosting Bridge

- Optional Discord webhook bridge for hosting design previews and uploaded images
- Removes the FiveM NUI size cap on large image uploads
- Required for AI Mode output hosting

---

## Framework Support

| Framework | Status |
|-----------|--------|
| QBCore | Full support |
| Qbox (qbx_core) | Full support |
| ESX (legacy) | Full support |
| Auto-detection | Included |

---

## Requirements

| Dependency | Notes |
|------------|-------|
| `oxmysql` | Database connector (required) |
| `ox_inventory` | Recommended on QBCore, required on Qbox |
| `qb-core` | If using QBCore framework |
| `es_extended` | If using ESX framework |
| Discord webhook URL | Required for AI image hosting and large user uploads |
| Google Gemini API key | Optional — only required if you want AI Mode enabled |

> ClothDesigner ships ready to run. No separate build step or extra installs are required.

---

## Quick Start

See [Installation](installation.md) for full setup instructions.
