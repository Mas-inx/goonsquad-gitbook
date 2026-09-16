# Usage

Run `/minimapdesigner` after receiving editor access. The command can be changed in `config/config.lua`.

---

## Your First Sheet

1. Give the sheet a name in the top bar. Use letters, numbers, underscores and hyphens, up to 64 characters.
2. Pick a base in **Style**: vanilla, atlas, satellite, or an installed custom pack.
3. Select a drawing tool, place an element, then adjust its properties in **Edit**.
4. Use **Layers** to select or hide individual elements.
5. Select **Save draft** to store the sheet.
6. Open **Radar** and select **Apply to my radar now** to preview baked imagery locally.
7. Select **Publish** and confirm to save the sheet and send it to all connected players.

{% hint style="info" %}
A saved draft does not replace the server's live sheet. A local radar preview is only visible to you. Publishing makes the design live and records it for future synchronization.
{% endhint %}

---

## Inspector Tabs

| Tab | Use |
|---|---|
| Layers | Browse, select and hide map elements |
| Edit | Change the selected element's properties |
| Style | Pick base imagery, recolour bands and adjust global or HUD colours |
| Postals | Configure the grid, numbering and appearance |
| Radar | Enable baking, preview, restore vanilla tiles or export a streaming resource |
| AI | Generate artwork or restyle a snip with the optional companion |

Hidden elements do not draw, bake or appear in game.

---

## Drawing Tools

| Tool | Behaviour |
|---|---|
| Blip | Places a native game marker with sprite, colour, scale and label |
| Circle / Rectangle | Draws a native radius or area blip |
| Polygon | Draws a custom filled shape that bakes into the map tiles |
| Path | Draws a line with width, dashes, optional arrowhead and halo |
| Text | Places a label with font, size, colour, rotation and halo |
| Image | Places an uploaded image with size, rotation, opacity and feathering |
| Snip | Copies a selected map region into an image element |
| Heal | Copies clean base imagery over a patch, with movable source and feathered edges |
| Measure | Measures distances on the canvas |

For polygons and paths, click to add vertices, then press **Enter** or double-click to finish. **Backspace** removes the last point while drawing; **Esc** cancels. A selected shape's vertices can be dragged, and right-clicking a vertex removes it.

---

## Styling and Baking

Colour bands pull pixels near a sampled colour toward a target colour. Adjust the tolerance to control the affected range. Global controls change hue, saturation, lightness and contrast across the imagery.

HUD colour overrides affect the map's vector colours separately.

Keep **Bake onto radar tiles** enabled for text, images, paths, polygons, heal patches, postals and recoloured imagery to appear on the live map. Native blips and circular or rectangular zones are handled separately.

**Restore vanilla** clears the local tile preview. To change what everyone sees or what loads next time, publish the intended sheet.

---

## Postals

Enable postals in the **Postals** tab. Frame the coverage area, set the number of rows and columns, and choose the origin corner and row- or column-first numbering.

You can set a prefix, starting number, zero-padding, rotation, label position, size and font, grid lines and cell fills. Publish the sheet when ready.

```text
/postal 100
```

Use the full displayed code, including a configured prefix or zero-padding. The command places a waypoint at that cell's centre. It does nothing when postals are disabled or the code is invalid.

---

## Sheet Catalogue

Open the folder button beside the sheet name to switch, rename, duplicate or delete sheets. The **LIVE** marker identifies the published sheet.

The active sheet cannot be deleted. Publish another sheet first. Use a new name for duplicates and renames: writing to an existing name can replace that saved design.

---

## Shortcuts

| Key | Action |
|---|---|
| `V` / `B` | Select / Blip |
| `C` / `R` / `O` | Circle / Rectangle / Polygon |
| `N` / `T` / `I` | Path / Text / Image |
| `S` / `H` / `M` | Snip / Heal / Measure |
| `K` / `P` / `A` | Style / Postals / AI |
| `G` | Toggle canvas grid |
| Arrow keys | Nudge selection; hold Shift for larger steps |
| `Ctrl+Z` / `Ctrl+Y` | Undo / Redo |
| `Ctrl+S` | Save draft |
| `Ctrl+D` | Duplicate selection |
| `Delete` | Delete selection |
| `Esc` | Cancel drawing, deselect, or close, depending on the current state |

For streaming output, continue with [Custom Maps & Streaming Exports](custom-maps.md). For image generation, see [AI Companion](ai.md).
