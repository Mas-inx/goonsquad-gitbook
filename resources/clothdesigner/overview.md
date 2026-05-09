# Goonsquad ClothDesigner

An in-game custom clothing studio for FiveM. Players walk up to a designer station, open a full-screen design canvas, paint, draw, type, upload, or **AI-generate** a graphic onto a real GTA garment, then save and print it as a wearable inventory item that anyone on the server can equip and see. Supports **QBCore**, **Qbox**, and **ESX** out of the box.

**Price:** TBD | [Buy on Tebex](https://goonsquad-inc.tebex.io/)

{% embed url="https://www.youtube.com/watch?v=Y5hUNsV0j3o" %}

---

## Features

### Studio (NUI)

- Full-screen, in-game design canvas with layers, transforms, undo/redo, fonts, shapes, and image uploads
- Live 3D preview of the design wrapped onto the actual GTA garment as you edit
- Library of every silhouette discovered on disk, filterable by gender / category, with "Pack full" lock overlays in real time
- "My Designs" panel showing every design the player has saved, with thumbnails
- Drag-and-drop image upload (PNG / JPEG / WebP, up to 4 MB by default)
- Asset URL import (any public HTTPS image URL)
- AI Mode: type a prompt, send the current UV template as a guide, get a Gemini-generated design back as a new layer

### Wearables

- Equipping a printed shirt swaps the player's shirt / pants / undershirt / shoes to the saved design — no character editor required
- DUI texture rendering: the design is composited at runtime so every layer (text, images, brushes) shows up exactly as it was saved
- All other players see the equipped design without a character refresh
- Equipped wearables persist across player disconnects, server restarts, and ped model swaps
- Auto-rehydrate from the player's current appearance if the state bag is missing (e.g. a clothing menu re-applied the same outfit)

### Pool / Storage Backend

- Multi-drawable pool packs: every silhouette is pre-allocated a drawable slot inside an MP DLC apparel pack at boot, so publishing a design is a pure DB op — no resource restart needed
- Auto-discovery of `cloth_templates/*.ydd` files at boot and on `/gscd_rescan`
- Auto-expansion: when a silhouette's 26 texture variants fill up, a new pack is materialised on the next restart
- Per-silhouette UI lock overlays so players can't start a session against a locked pack
- Idempotent boot: subsequent restarts are instant once the pool is materialised
- Discord webhook bridge for asset hosting (avoids FiveM's NUI packet size limits for large images)

### Framework Integration

- Auto-detects QBCore, Qbox (qbx_core + ox_inventory), or ESX
- Uses `ox_inventory` for shirt items when available; falls back to QBCore / ESX inventory otherwise
- Designer-station access is gated by job + grade (configurable per job)
- Printed shirts use ox_inventory `imageurl` so designs show their own preview as the inventory thumbnail when ox_inventory is the backend

### Persistence & Recovery

- Every design is versioned with previous-version preview rows kept on disk
- Equipped wearables stored in `gs_clothdesigner_active_wearables` and restored on framework `playerLoaded`
- Audit log for design create / publish / delete actions

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
| Node.js 22 (FiveM artifact built-in) | Used by the silhouette discovery + pool allocator |
| `ox_inventory` | Recommended on QBCore, required on Qbox |
| `qb-core` | If using QBCore framework |
| `es_extended` | If using ESX framework |
| Discord webhook | Required for AI image hosting + large user uploads |
| Google Gemini API key | Optional, only required if you want the AI Mode tool enabled |

> ClothDesigner uses FiveM's built-in `node_version '22'` from the manifest. No separate Node install or build step is required for normal use — the Node bundle ships pre-built in `dist/server/silhouettes.js`.

---

## Quick Start

See [Installation](installation.md) for full setup instructions.
