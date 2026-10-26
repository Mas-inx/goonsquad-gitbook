# Inventory Integration

By default a speaker is an item (`Config.Boombox.item.required = true`, `select = 'items'`): a
player buys one at the speaker shop (`Config.Shop`) and uses it from their inventory to put it
down; putting it away gives the item back. Admins can always use `/boombox`. With
`required = false` no item is needed: `/boombox` works for everyone `Config.Permissions.use`
allows.

- **ESX / QBCore / QBox (their own inventories):** create an item called `boombox` the usual way.
  The resource registers it as usable by itself (again, if the framework is restarted). With
  `consume = true` it is taken when the boombox is placed and given back when it is put away.
- **ox_inventory:** add to `ox_inventory/data/items.lua`:
  ```lua
  ['boombox'] = { label = 'Boombox', weight = 2000, stack = false, close = true,
      server = { export = 'gs-boombox-carplay.useBoombox' } },
  ['carplay'] = { label = 'Media system kit', weight = 1000, stack = true, close = true,
      server = { export = 'gs-boombox-carplay.useCarplay' } },
  ```

**A model as an item of its own** (`Config.Boombox.select = 'items'` or `'both'`): each model's
`item` in `config/props.lua` (`gs_boombox_rgb`, `gs_boombox_retro`, `gs_speaker_set`,
`gs_party_speaker`, `gs_speaker_cyl`) is an item that puts that model down; with `consume` it is
that item that is taken and that comes back. ESX / QBCore / QBox register them by themselves; for
ox_inventory wire each to the same export:
```lua
['gs_speaker_cyl']   = { label = 'Cylinder Speaker', weight = 1500, stack = false, close = true, server = { export = 'gs-boombox-carplay.useBoombox' } },
['gs_boombox_rgb']   = { label = 'RGB Boombox', weight = 3500, stack = false, close = true, server = { export = 'gs-boombox-carplay.useBoombox' } },
['gs_boombox_retro'] = { label = 'Retro Boombox', weight = 4500, stack = false, close = true, server = { export = 'gs-boombox-carplay.useBoombox' } },
['gs_party_speaker'] = { label = 'Party Speaker', weight = 15000, stack = false, close = true, server = { export = 'gs-boombox-carplay.useBoombox' } },
['gs_speaker_set']   = { label = 'Hi-Fi 2.1 Set', weight = 12000, stack = false, close = true, server = { export = 'gs-boombox-carplay.useBoombox' } },
```
Their pictures: the release includes `inventory-images/*.png` (named after the items), copied into
`ox_inventory/web/images/`.

An item that was taken is never simply lost. If the boombox is put away while its owner is not
there to be given the item (they disconnected, switched character, their inventory is full, the
server restarted or crashed), the item is kept with their saved data and handed over the next
time they join, load that character, or place a boombox. The one gap: a server that crashes
within `flushSeconds` (20 s) of a boombox being placed has not written it down yet.

For cars, `Config.Car.availability = 'installed'` makes the system exist only in vehicles where
the `carplay` item was used while sitting in them (remembered by plate).

**The car screens** (`Config.Car.screens`) are items too. ESX / QBCore / QBox register them by
themselves; for ox_inventory:
```lua
['gs_carplay_portable'] = { label = 'GS Drive 7" portable', weight = 900, stack = false, close = true, server = { export = 'gs-boombox-carplay.useScreen' } },
['gs_carplay_wide']     = { label = 'GS Drive 10" wide', weight = 1300, stack = false, close = true, server = { export = 'gs-boombox-carplay.useScreen' } },
['gs_carplay_dindin']   = { label = 'GS Drive 10" double-DIN', weight = 1600, stack = false, close = true, server = { export = 'gs-boombox-carplay.useScreen' } },
['gs_carplay_portrait'] = { label = 'GS Drive 12" vertical', weight = 1800, stack = false, close = true, server = { export = 'gs-boombox-carplay.useScreen' } },
```
Their pictures are `inventory-images/gs_carplay_*.png`, copied into `ox_inventory/web/images/`.
The item is taken when the screen is fitted and comes back when it is taken out.

## Release Item Images

Copy the nine PNG files from `gs-boombox-carplay/inventory-images/` into your inventory's image folder, retaining the filenames. The generic `boombox` and `carplay` aliases can use your own icons. Item images are installation assets, not editable web source.

The built-in shop uses `ox_inventory`; configure the items before enabling it. With another inventory, set `Config.Shop.enabled = false` and distribute the items through your own shops. Adapt `server/bridge.lua` for unsupported inventory APIs. Failed item grants must return false so refunds remain pending.
