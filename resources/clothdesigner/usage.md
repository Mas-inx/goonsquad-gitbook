# Usage

---

## Player Guide

### Opening the Studio

1. Get a job that's listed in `Config.Designer.jobs` (default: `clothingdesigner` or `ambulance`).
2. Walk up to a designer station. The default station is at `-1194.96, -767.87, 17.32` (the clothing store on Hawick Ave). A blue marker shows you where it is.
3. Press **E** when prompted.

The studio opens in fullscreen with three panes:

| Pane | What's in it |
|------|--------------|
| **Left** | Library of every available silhouette and **My Designs** (your saved designs) |
| **Center** | The design canvas — a 1024×1024 surface with a faint UV guide showing where the print area sits on the actual garment |
| **Right** | Live 3D preview of the current canvas wrapped onto the silhouette |

### Starting a Design

1. In the **Library** pane, filter by gender and category (Tops, Bottoms, Undershirts, Shoes).
2. Click a silhouette card to start designing.
3. The 3D preview swaps to that silhouette and a fresh canvas loads with the silhouette's UV guide.

> If a silhouette card shows a **Pack full** overlay, all of its slots are temporarily occupied — pick a different one or wait for the next server restart.

### Drawing Tools

The toolbar across the top of the canvas covers:

| Tool | Use |
|------|-----|
| **Brush** | Freehand paint with adjustable size and colour |
| **Shapes** | Rectangle, ellipse, line, polygon — drag on the canvas to place |
| **Text** | Click to place a text layer; pick a font from the dropdown |
| **Image** | Drag-and-drop an image, paste a URL, or pick from the asset library |
| **AI** | Type a prompt; the server generates a design that's added as a new image layer |
| **Eraser / Selection** | Standard transform handles, undo/redo, layer reordering |

Layers are listed in the right side of the toolbar. Click a layer to select it, drag to reorder. The right panel shows transform properties (position, scale, rotation, opacity) for the selected layer.

### Live 3D Preview

Every change you make on the canvas updates the 3D preview within a frame. Rotate the model by dragging in the preview pane. The preview uses the actual garment you'll be wearing, so what you see is what you get when you save and equip.

### Saving & Printing

- **Save** stores the design as a draft. You can come back and edit it later from **My Designs**.
- **Save & Print** does the same as Save, then publishes the design and adds a printed shirt item to your inventory.

Once printed, the design is published and ready to wear. You can use the shirt item, hand it to another player, or sell it.

### Wearing a Custom Shirt

1. Open your inventory and **use** a printed shirt.
2. Your character's relevant component (top / bottoms / undershirt / shoes — depends on which silhouette the design was made for) instantly swaps to the design.
3. Other players see the design in real time, no character refresh required.

The custom texture stays on you across:

- Server restarts
- Disconnects and reconnects
- Ped model swaps (the texture is re-applied if you swap to another supported MP freemode model)

### Removing a Custom Shirt

Equip a different outfit (any clothing menu, character editor, or job uniform) and the custom shirt for that component clears automatically.

To wipe **all** custom textures from your character at once, you can wire a "Clear outfit" button in your menu — see [API & Exports](exports.md).

---

## AI Mode

> AI Mode requires `Config.AI.enabled = true` plus a `GoogleApiKey` and `DiscordWebhookUrl` in `server/credentials.lua`. See [Configuration](configuration.md).

1. Click the **AI** tool in the toolbar.
2. Type a prompt describing what you want, e.g. *"neon cyberpunk dragon wrapping around the torso, holographic blue and pink"*.
3. Click **Generate**.

A few seconds later, the result is added as a new image layer on your canvas. The output is a normal layer — you can move it, scale it, paint over it, or delete it like anything else.

The current canvas is sent along with the prompt as a guide, so the AI knows where the garment is and paints inside the print area instead of generating a free-form image.

---

## Capacity & Auto-Expansion

ClothDesigner is designed to host effectively unlimited designs. The system pre-allocates space for every garment template you have, and **automatically expands** when any individual silhouette runs out of slots — no manual intervention needed.

If a silhouette card shows a **Pack full** overlay, just restart the server. Auto-expansion runs on every boot and the lock clears automatically.

> Adding new garment templates? Just drop the `.ydd` files in and restart — the boot routine handles discovery, expansion, and apparel preparation in one go. See [Installation → Adding More Cloth Templates](installation.md#adding-more-cloth-templates).

---

## Admin Commands

All commands are server-console / RCON only by default. Add ACE permissions if you want to expose them in-game (see [Installation → Permissions](installation.md#permissions-optional)).

| Command | Description |
|---------|-------------|
| `gscd_delete_design [designId]` | Delete a published design and unequip it from any online player wearing it. |
| `gscd_rescan` | *(Advanced)* Re-scan `cloth_templates/` for new garment templates without restarting. A server restart still does the same thing automatically. |
| `gscd_rebuild_pool` | *(Advanced)* Re-prepare the apparel pool without restarting. A server restart still does the same thing automatically. |

> For day-to-day use, you'll rarely (if ever) need the rescan / rebuild commands — restarting the server triggers everything they do, plus the final apparel registration step. They're included for hot-reload scenarios where a restart is inconvenient.

---

## Common Fixes

| Problem | Fix |
|---------|-----|
| Pack-full overlay on a silhouette | Restart the server — auto-expansion runs on boot. |
| Just dropped new `.ydd` files | Restart the server. |
| AI Mode error in the UI | Check server console — the most common cause is a missing `GoogleApiKey` or `DiscordWebhookUrl`. |
| Player rejoined and lost their custom textures | Restart `gs-clothdesigner`. |
