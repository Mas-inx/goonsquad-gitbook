# Adding Vehicles

GS Vehicle Designer ships with no cars. You supply them, and the resource converts them into livery-capable vehicles automatically.

The whole workflow is: drop vehicle files into `vehicle_templates/`, restart the server, restart once more when the console asks.

---

## 1. Drop In Templates

Everything goes under:

```text
gs-vehicledesigner/vehicle_templates/
```

Three layouts are accepted and can be mixed freely in the same folder:

| Layout | Description |
|---|---|
| A whole car pack | Copy the pack in as-is. Its existing `stream/` and `data/` structure works unchanged. |
| A single vehicle resource | Copy the resource folder in. |
| A per-model folder | `vehicle_templates/<model>/` holding the model's `yft`, `_hi.yft`, and `ytd` next to its meta files. |

A model is discovered when some `vehicles.meta` in the tree declares it **and** the matching `<model>.yft` plus `<txdName>.ytd` exist somewhere in the tree. The folder depth and organisation do not matter.

Tuning-part YFTs, wheel YDRs, animation YCDs, `+hi` YTDs, and other shared assets found alongside a model are carried along verbatim, so a pack's cars keep their mods and wheels.

---

## 2. Restart Twice

On the first boot after new templates appear, the resource scans, converts, and writes the generated assets. It then prints:

```text
==========================================================
[gs-vehicledesigner] New vehicle assets were materialised.
Restart the server for the vehicles to take effect.
(Subsequent restarts will be instant - no action needed.)
==========================================================
```

Restart once more so FiveM streams the newly written files.

Heavy conversion runs after startup completes, so the resource boot itself is not blocked and the server keeps sending heartbeats while vehicles are processed.

---

## What Gets Generated

Per vehicle, the resource writes inside its own folder:

| Path | Contents |
|---|---|
| `stream/<model>/` | Converted `yft`, `_hi.yft`, and `<txd>.ytd`, plus the pack's extra streamables |
| `data/<model>/` | `vehicles.meta`, `handling.meta`, `carvariations.meta`, `carcols.meta`, and `signature.json` |
| `uv_output/<model>/` | The NUI preview YFT and the semantic design guide image |
| `data/vehicles.json` | The designer catalog, with per-model labels and semantic region data |

Nothing is written back into `vehicle_templates/`. Your source files are left untouched.

---

## How Conversion Works

Every paint material on the vehicle (`vehicle_paint1` through `vehicle_paint4`) is converted so it can carry a livery texture, injecting a UV1 channel where the mesh lacks one. Seventeen native livery slots are registered and shipped fully transparent, so an unpainted spawn shows plain paint.

All faces are projected into one **fixed semantic layout** — left, right, top, underbody, front, and rear regions that are identical on every vehicle. This is what makes a design's placement predictable: a stripe drawn across the door of one car lands in the same part of the canvas on every other car.

UV0 and all other vertex data are preserved byte-for-byte and validated after conversion. A vehicle whose paint shaders cannot be converted is reported per-model rather than failing silently.

---

## Content Signatures

Each converted vehicle carries a content signature at `data/<model>/signature.json`. On every later boot the resource compares signatures and skips anything unchanged, so restarts after the first materialisation are effectively instant.

- Changing a template reconverts only that vehicle.
- Removing a template sweeps its generated assets on the next boot.
- The published designs and pool row for a removed vehicle are preserved, so they come back intact if the template returns.

---

## Live Slots

Each model gets 16 live finish slots. Publishing a design claims the first free slot; deleting or unpublishing releases it.

Occupancy lives in a database bitmap in `gs_vehicledesigner_pool_vehicles`, and the effective occupancy always includes every published design, so the bitmap cannot drift out of sync with reality. Allocation and release are pure database operations, with no disk writes and no restart.

Check occupancy from the server console:

```text
gsvd_pool_status
```

```text
[gsvd][pool] 3 provisioned vehicle(s):
  viper: 14/16 slots free
  supra19: 0/16 slots free (LOCKED)
  gtr: 16/16 slots free
```

---

## Series Clones

When a vehicle **and** every existing clone of it are fully occupied, the next rebuild automatically provisions a **series clone**: a fresh spawnable copy named `<model>_2`, then `_3`, and so on, each with its own 16 live slots.

Clones are cheap to produce. The converted YFTs are reused byte-for-byte, only the YTD slots and two meta files are regenerated, and the clone references the base vehicle's kits, lights, and handling so nothing is registered twice.

A clone is an independent spawn code. Designs published on `viper_2` fit `viper_2` vehicles, not `viper`. In the studio catalog a clone inherits its base label as *"<Label> (Series 2)"*.

Force one at any time without waiting for the slots to fill:

```text
gsvd_expand viper
```

Clones follow their base template's lifecycle: they re-materialise when the base changes and are swept when the base template is removed.

---

## Console Commands

All of these are restricted commands. Grant them as shown in [Installation](installation.md#9-permissions).

| Command | Purpose |
|---|---|
| `gsvd_rescan` | Rescan `vehicle_templates/` and sync the catalog, without converting |
| `gsvd_rebuild_pool` | Rescan and run a full materialisation pass |
| `gsvd_pool_status` | Print per-model slot occupancy and lock state |
| `gsvd_expand <model>` | Force-mint the next series clone for a model and materialise it |
| `gsvd_fill_slots <model>` | Mark every slot for a model as occupied, to test the lock flow |

{% hint style="danger" %}
`gsvd_fill_slots` is a development command. It deliberately exhausts a vehicle's slots and should not be granted on a production server.
{% endhint %}

---

## Pre-Materialising Offline

The same pipeline runs outside FiveM, which is useful for preparing a build before shipping it to a live server. From the resource folder:

```bash
npm run pool:rebuild
```

To scan and report without converting anything:

```bash
npm run pool:scan
```

Both run the exact discovery and materialisation the server runs on boot, with no FiveM and no database required. Ship the generated `stream/`, `data/`, and `uv_output/` folders with the resource and the first live boot has nothing left to do.

---

## Troubleshooting Templates

If a vehicle does not appear in the studio, see [Troubleshooting](troubleshooting.md#vehicles-and-the-pool).
