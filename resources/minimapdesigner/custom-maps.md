# Custom Maps & Streaming Exports

Custom packs provide editable base imagery. Streaming exports turn the baked imagery into a separate resource.

---

## Import a Map Pack

Place each pack in a direct subfolder of `custom_maps/`:

```text
gs-minimapdesigner/
  custom_maps/
    my-postal-map/
      minimap_0_0.ytd
      minimap_0_1.ytd
      minimap_1_0.ytd
      minimap_1_1.ytd
      minimap_2_0.ytd
      minimap_2_1.ytd
```

Use letters, numbers, underscores and hyphens for the pack folder name, up to 64 characters. Put the YTDs directly in that folder; discovery does not walk deeper directories.

Restart the resource and select the pack under **Style → Base imagery**. The editor reads the texture dictionaries and assembles textures named `minimap_<row>_<col>` or `minimap_sea_<row>_<col>` into the map grid.

The texture names inside the YTDs matter. Renaming an unrelated YTD file does not make its textures a compatible map.

{% hint style="info" %}
The browser needs readable texture dictionaries. Keep imported packs available in `custom_maps/` for every server using a sheet based on that pack.
{% endhint %}

---

## Export Baked Imagery

1. Open the sheet you want to export.
2. Open **Radar** and find **Export as stream resource**.
3. Enter a unique resource name, up to 64 letters, numbers, underscores or hyphens.
4. Select **Export .ytd resource** and wait for completion.
5. Find the folder on the server at `gs-minimapdesigner/exports/<name>/`.

The generated folder contains:

```text
my_minimap/
  fxmanifest.lua
  README.md
  stream/
    minimap_0_0.ytd
    ...
    minimap_lod_128.ytd
```

Tiles are baked at 2048 × 2048 with DXT1 compression and mipmaps. A separate 128 × 128 texture supplies the far-zoom land-map view.

{% hint style="warning" %}
Exporting again with the same name clears and rebuilds that folder. Copy or rename any output you want to retain before reusing a name.
{% endhint %}

---

## Install the Export

Copy the generated folder out of the designer's `exports/` directory and into the server's resources directory:

```cfg
ensure my_minimap
```

For the stock map grid, the exported textures can run without the designer. Avoid competing resources streaming textures with the same names, and clear live designer previews when comparing the export.

### What the Export Includes

It contains the baked raster imagery: the selected base, colour adjustments, polygon zones, paths, text, images, heal patches and postal artwork.

Native blips, circular and rectangular zone blips, HUD colour overrides, `/postal` routing and the postal HUD are runtime features. They are not recreated by the texture-only export. Keep the designer running with the relevant design, or implement those features in your own integration.

### Cayo Perico

Extra cells outside the stock grid use `gs_tile_r*_c*.ytd` names. Their live preview is handled by the designer, but the generated streaming resource does not include a standalone drawer for those cells.

Read the generated `README.md` for the extra-cell coordinates and renderer configuration. Stock-grid installation instructions alone are insufficient for those extra tiles.
