# Usage

---

## Player Guide

### Opening the Studio

1. Get a job that's listed in `Config.Designer.jobs` (default: `clothingdesigner` or `ambulance`).
2. Walk up to a designer station. The default station is at `-1194.96, -767.87, 17.32` (the clothing store on Hawick Ave). A blue marker shows you where it is.
3. Press **E** when prompted.

The studio opens in a fullscreen NUI with three panes:

| Pane | What's in it |
|------|--------------|
| **Left** | Library (every silhouette discovered on disk) and **My Designs** (your saved designs) |
| **Center** | The design canvas — a 1024×1024 surface with a faint UV guide showing where the print area sits on the actual garment |
| **Right** | Live 3D preview of the current canvas wrapped onto the silhouette |

### Starting a Design

1. In the **Library** pane, filter by gender and category (Tops, Bottoms, Undershirts, Shoes).
2. Click a silhouette card. If the card has a **Pack full** lock overlay, all 26 texture slots for that silhouette are already taken — pick a different one or wait for the server to auto-expand on next restart.
3. The 3D preview swaps to that silhouette and a fresh canvas loads with the silhouette's UV guide.

### Drawing Tools

The toolbar across the top of the canvas covers:

| Tool | Use |
|------|-----|
| **Brush** | Freehand paint with adjustable size and colour |
| **Shapes** | Rectangle, ellipse, line, polygon — drag on the canvas to place |
| **Text** | Click to place a text layer; pick a font from the dropdown |
| **Image** | Drag-and-drop an image, paste a URL, or pick from the asset library |
| **AI** | Type a prompt; the server generates a design with Gemini using the current UV template as a guide |
| **Eraser / Selection** | Standard transform handles, undo/redo, layer reordering |

Layers are listed in the right side of the toolbar. Click a layer to select it, drag to reorder. The right panel shows transform properties (position, scale, rotation, opacity) for the selected layer.

### Live 3D Preview

Every change you make on the canvas updates the 3D preview within a frame. Rotate the model by dragging in the preview pane. The preview uses the actual `.ydd` you'll be wearing, so what you see is what you get when you save and equip.

### Saving & Printing

- **Save** stores the design as a draft. You can come back and edit it later from **My Designs**.
- **Save & Print** does the same as Save, then publishes the design (locks in a texture slot in the pool) and adds a printed shirt item to your inventory.

Once printed, the design is "published" and ready to wear. You can use the shirt item, hand it to another player, or sell it.

### Wearing a Custom Shirt

1. Open your inventory and **use** a printed shirt.
2. Your character's relevant component (top / bottoms / undershirt / shoes — depends on which silhouette the design was made for) instantly swaps to the design.
3. Other players see the design in real time, no character-refresh required.

The custom texture stays on you across:

- Server restarts
- Disconnects and reconnects
- Ped model swaps (the texture is re-applied to the new ped if it's a supported MP freemode model)

### Removing a Custom Shirt

Equip a different outfit (any clothing menu, character editor, or job uniform) and the GSCD wearable for that component clears automatically.

To wipe **all** custom textures from your character at once, the server-side export `clearWearableState` can be triggered (e.g. from a "Clear outfit" button in your menu).

---

## AI Mode

> AI Mode requires `Config.AI.enabled = true` plus a `GoogleApiKey` and `DiscordWebhookUrl` in `server/credentials.lua`. See [Configuration](configuration.md).

1. Click the **AI** tool in the toolbar.
2. Type a prompt describing what you want, e.g. "neon cyberpunk dragon wrapping around the torso, holographic blue and pink".
3. Click **Generate**.

What happens behind the scenes:

1. The studio downsamples the current canvas + UV template to a 512×512 JPEG and sends it to the server.
2. The server forwards your prompt + UV guide to Gemini, asking it to paint **directly onto the UV** (not show the UV).
3. Gemini returns a 1024×1024 PNG.
4. The server saves it to disk, uploads it to the configured Discord webhook (so the URL works for every other client too), and sends the URL back.
5. The studio adds the result as a new image layer on your canvas.

The AI output is a normal layer — you can move it, scale it, paint over it, or delete it like anything else.

---

## Pool Lifecycle (For Server Admins)

ClothDesigner manages a "pool" of multi-drawable apparel packs in the background. Understanding the lifecycle helps you reason about when a server restart is needed.

### How packs work

- Each pack is one MP DLC apparel collection holding up to 16 silhouettes.
- Each silhouette inside a pack has up to 26 texture variants (`a..z` letters).
- Total capacity per pack: **16 silhouettes × 26 textures = 416 published designs**.
- Capacity scales with the number of packs; new packs are auto-allocated when the existing ones fill up.

### Boot flow

1. Discovery scans `cloth_templates/` and writes / updates `gs_clothdesigner_silhouettes`.
2. The pool allocator groups silhouettes by `(gender, category, has_skin)` and assigns each one a drawable slot in a pack.
3. New packs are materialised to disk (`generated/`, `stream/generated/`, `fxmanifest.lua` is updated between the auto-managed markers).
4. Subsequent boots are instant — the allocator skips materialisation when files already exist on disk.

### Publishing a design

1. Lua asks the JS allocator for a free texture slot in this silhouette's pack(s).
2. JS flips a bit in `gs_clothdesigner_pack_drawables.texture_slot_bitmap`, returns the descriptor.
3. Lua saves the design row pointing at that slot.
4. Done. **No restart required.** The shirt item is added to the player's inventory.

### Filling up a silhouette

When all of a silhouette's drawable assignments have used all 26 texture variants:

- The library shows a **Pack full** overlay on that silhouette card and gates session start.
- On the **next** server restart, the pool allocator detects the full silhouette and adds a new drawable slot for it (in a new pack if necessary).
- The lock overlay clears automatically once the pool is rebuilt.

You can trigger expansion immediately with `gscd_rebuild_pool` instead of waiting for the next restart.

---

## Admin Commands

All commands are server-console / RCON only by default. Add ACE permissions if you want to expose them in-game (see [Installation](installation.md#permissions)).

| Command | Description |
|---------|-------------|
| `gscd_rescan` | Re-scan `cloth_templates/` and sync the silhouette table. Run after dropping new `.ydd` files in. |
| `gscd_rebuild_pool` | Re-run the full pool allocator. Materialises new packs, expands locked silhouettes. Use after `gscd_rescan` or when troubleshooting. |
| `gscd_fill_slots [source_key]` | Debug helper. Fills every texture variant for a silhouette so you can test the lock overlay + auto-expansion. Omit `source_key` to target the first silhouette in the catalog. Example: `gscd_fill_slots male/tops/jbib_000_u.ydd`. |
| `gscd_delete_design [designId]` | Delete a published design, free its slot, and unequip it from any online player wearing it. |
| `gscd_test_reload` | Client-side diagnostic. Prints whether the active GSCD collection is recognised by GTA on this client. Useful when troubleshooting why a wearable isn't applying. |

Example output of `gscd_rebuild_pool`:

```
[gs-clothdesigner] Pool rebuilt:
  silhouettes: 64
  packs:       6 total, +1 new
  drawables:   +0 new assignments, 1 auto-expanded
  on-disk:     16 materialised, 0 skipped, 0 orphans, 0 failed
  resource:    started
```

---

## Player Commands

| Command | Description |
|---------|-------------|
| `/gscd_test_reload [collection]` | Diagnostic only. Prints whether a GSCD collection is recognised by your local game. Pass a collection name as argument or wear a custom design first. |

---

## Troubleshooting Without Restarting

Most issues can be resolved without a full server restart:

| Problem | Try |
|---------|-----|
| Pool full overlay on every silhouette | `gscd_rebuild_pool` then restart once |
| Just dropped new `.ydd` files | `gscd_rescan`, then `gscd_rebuild_pool`, then restart once |
| Design saved but won't apply on a player | `gscd_test_reload` on that player's client. If it returns `false`, the pack hasn't loaded — likely needs the post-materialisation restart |
| AI Mode error in the UI | Check server console for `[gs-clothdesigner]` errors. The most common cause is missing `GoogleApiKey` or `DiscordWebhookUrl`. |
| Player rejoined and lost their custom textures | Confirm `gs_clothdesigner_active_wearables` has rows for that player's identifier. If yes, restart `gs-clothdesigner` to re-trigger rehydration. |
