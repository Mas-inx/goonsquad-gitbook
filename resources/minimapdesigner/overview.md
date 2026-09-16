# GS Minimap Designer

GS Minimap Designer is an in-game map design studio for FiveM. Build blips, zones, labels, images and postal grids over the map, recolour its imagery, preview the result on your radar, and publish it to everyone on the server.

These pages document version **0.2.0**.

**Price:** $19.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7679716)

{% embed url="https://www.youtube.com/watch?v=PfnapYIN6AY" %}

{% hint style="info" %}
The release includes the compiled interface and the map renderer needed for live tile previews. Configuration, credentials, storage and media bridges, and postal integration remain editable under Asset Escrow. See [Configuration](configuration.md#file-layout).
{% endhint %}

---

## Highlights

### Design Studio

- Pan and zoom over vanilla map tiles, atlas imagery, satellite imagery, or an imported map pack
- Place native blips with a searchable sprite picker, colour, scale and short-range settings
- Draw circles, rectangles, polygons and paths with editable vertices
- Add text, images, snips and feathered heal patches to the map imagery
- Manage layers, hide individual elements, duplicate selections, and undo or redo edits
- Save multiple sheets with thumbnails and a catalogue that marks the published sheet as **LIVE**

### Map Styling and Postals

- Remap individual colour bands and adjust global hue, saturation, lightness and contrast
- Override HUD map colours separately from the baked imagery
- Frame, rotate and number a postal grid, with configurable labels, lines and cell fills
- Route with `/postal <code>` and show the current postal in a configurable HUD readout
- Bake optional extra cells covering Cayo Perico for the live map

### Publishing and Integration

- Preview tiles locally, then publish the design to connected players
- Synchronize the published design on resource start and, when enabled, player spawn
- Store sheets in JSON files or an automatically created database table
- Export baked imagery as a separate streaming resource
- Read the published design and postal coordinates through exports
- Optionally generate or restyle map artwork with the separate `gsmd-ai` companion resource

---

## Compatibility

| Environment | Support |
|---|---|
| Standalone | Supported |
| QBCore / Qbox / ESX | Supported without a framework bridge |
| Custom HUD | The HUD retains control of radar shape, size and position |
| Database storage | Optional `oxmysql` adapter |
| AI generation | Optional `gsmd-ai` companion |
| In-game AI captures | Optional `screenshot-basic` resource |

Do not run another resource streaming `minimap_main_map.gfx` alongside the designer. Competing copies of that asset can prevent the bundled renderer from working.

---

## Requirements

- A current FiveM server artifact with Node.js 22 support
- Editor access granted through an ACE or the server-only identifier list
- Writable resource storage for JSON designs, thumbnails and exported resources
- Optional `oxmysql` for MySQL storage
- Optional media credentials for hosted images

The released resource needs no npm installation or UI build on the server.

---

## Next Steps

1. Follow [Installation](installation.md).
2. Review [Configuration](configuration.md), especially access and storage.
3. Create and publish your first sheet with [Usage](usage.md).
4. Bring in map packs through [Custom Maps & Streaming Exports](custom-maps.md).
5. Connect your HUD or scripts through [API & Exports](exports.md).
