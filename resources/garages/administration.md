# Administration

GS Garages includes an in-game admin suite and a server command for migrations, imports, and maintenance.

---

## Access

A player is an administrator when any of the following is true:

- They hold the ACE in `Config.Admin.ace` (default `goonsquad.admin`)
- They hold `command.gs_garages`
- They are the server console

Recommended ACE:

```cfg
add_ace group.admin goonsquad.admin allow
```

{% hint style="danger" %}
Garage administrators can edit live configuration, force garages open on players, and impound vehicles. Grant `goonsquad.admin` only to trusted staff.
{% endhint %}

---

## In-Game Admin Panel

`/garageadmin` (configurable via `Config.Admin.command`) opens the admin panel.

### Garages Tab

Create, edit, disable, and restore garages without touching a file:

- **World placement.** The interaction point and each spawn bay are placed by aiming in the world: the panel drops focus, you get a crosshair ghost (a full ghost car for spawn bays, scroll wheel to set the heading), and the result lands back in the form.
- Every save is validated server-side, persisted to the `gs_garages` table, merged over `config/garages.lua`, and pushed live to every connected client — no restart.
- Editing a config-file garage **overrides** it (restorable). Deleting a config-file garage **tombstones** it (restorable). Garages created in game are rows of their own.

`config/garages.lua` stays the seed and stays in version control; the database holds the diffs.

### Settings Tab

Nearly every option in `config/config.lua` — fees, radii, features, housing, camera, overlays, prompts, rate limits, logging — is editable live, with sliders, typed validation, per-option reset, and restart-required flags where needed.

- Values persist to the `gs_settings` table and re-apply on every boot.
- Overridden options are marked with a dot; the reset button restores the file default.
- Options marked server-scope (webhook URLs and the like) never leave the server.
- The adapter selection (`Config.Framework`, `Config.Fuel`, …), the override hooks, and the migration mappers are deliberately file-only.

{% hint style="info" %}
Once an option has a live override, editing the same value in `config/config.lua` has no visible effect — the database row wins on every boot. Reset the override from the panel to hand control back to the file.
{% endhint %}

---

## Commands

Run from the server console, or in game with the admin permission. `gsgarage` is an alias for the same command with the same permission check.

| Command | What it does |
|---|---|
| `gs_garages status` | Show the detected framework, key provider, garage count, and resolved column map |
| `gs_garages migrate` | Re-run the idempotent schema migration |
| `gs_garages import` | Dry run of the native import — prints what would change |
| `gs_garages import --apply` | Apply the native import |
| `gs_garages mappers` | List available cross-script mappers |
| `gs_garages import <mapper>` | Dry run a cross-script import (e.g. `gs_garages import qb-garages`) |
| `gs_garages import <mapper> --apply` | Apply a cross-script import |
| `gs_garages open <playerId> <garageId>` | Force a garage open on a player |
| `gs_garages impound <plate> [price]` | Send a vehicle to the depot, optionally with a custom release price |

---

## Migration Workflow

All schema work is **additive and idempotent** — columns are only ever added, never dropped or renamed, and running anything twice changes nothing the second time.

### Native import

Fills in what dealerships left blank on the framework's own rows: NULL or unknown garage → `Config.DefaultGarage`, NULL state → parked, NULL condition → full, missing category → inferred, missing model → recovered from the props blob.

```text
gs_garages import
gs_garages import --apply
```

The dry run prints `scanned / updated / skipped` counts plus a sample of the exact changes.

### Migrating from Another Garage Resource

Mappers ship for:

- qb-garages
- jg-advancedgarages
- cd_garage
- loaf_garage

```text
gs_garages import qb-garages           # dry run
gs_garages import qb-garages --apply   # write it
```

Each mapper checks that its source table and columns exist and skips with a clear message if not — it never errors, and it never writes to the source script's tables. Garage-id mapping is part of the mapper, so vehicles land in your matching GS Garages ids.

{% hint style="warning" %}
Back up the database before applying any migration, and always read the dry run first.
{% endhint %}

---

## Impound Operations

Police and tow resources normally impound through the export:

```lua
exports['gs-garages']:ImpoundVehicle(plate, price, garageId)
```

`price` sets the depot release fee for that vehicle; `garageId` selects the lot (defaults to `Config.DefaultImpound`). If the vehicle is currently on the map, its entity is deleted. Release permissions, free-for-jobs, and the release destination are governed by `Config.Impound` — see [Configuration](configuration.md#impound).

---

## Discord Logging

With `Config.Logs.enabled` and a webhook set, retrieve, store, transfer, impound release, key-sharing, and migration events post to Discord in batches. Which events post is configurable per event. The webhook URL is server-scoped and never sent to clients.
