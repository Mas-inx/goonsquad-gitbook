# Installation

## Step 1: Requirements

GS Garages requires:

- **ox_lib**
- **oxmysql**
- MySQL 8+ or MariaDB 10.4+

A framework, fuel script, key script, target script, and housing script are all **optional** — each is auto-detected and degrades cleanly when absent.

{% hint style="warning" %}
Keep the folder name exactly `gs-garages`. Other resources call its exports using that name, and the NUI is served from it.
{% endhint %}

---

## Step 2: Add to Server

1. Copy the `gs-garages` folder into your server's resources directory.
2. Make sure `oxmysql`, `ox_lib`, your framework, and any optional integrations start **before** it.

Example Qbox order:

```cfg
ensure oxmysql
ensure ox_lib
ensure qbx_core
ensure ox_target
ensure qbx_vehiclekeys
ensure gs-garages
```

Example QBCore order:

```cfg
ensure oxmysql
ensure ox_lib
ensure qb-core
ensure qb-target
ensure qb-vehiclekeys
ensure gs-garages
```

Example ESX order:

```cfg
ensure oxmysql
ensure ox_lib
ensure es_extended
ensure gs-garages
```

Only `ox_lib` and `oxmysql` are hard dependencies. Remove optional lines that your server does not use.

---

## Step 3: First Start

No SQL import is required. On first start, GS Garages detects the framework and migrates the vehicle table automatically:

```text
[gs-garages] framework detected: QBCore
[gs-garages] migration: added `player_vehicles`.`gs_favorite` (TINYINT(1) NOT NULL DEFAULT 0)
[gs-garages] schema ready on `player_vehicles` (17 columns mapped, 4 added)
[gs-garages] ready — 13 garage(s), framework QBCore
```

Migrations are **additive and idempotent**: columns are only ever added, never dropped or renamed, and only after checking `INFORMATION_SCHEMA`. Running the migration twice changes nothing the second time. On ESX, the new `state` column is seeded from ESX's own `stored` column the one time it is created, and every later write keeps `stored` in sync so vanilla ESX scripts keep working.

Two companion tables are created: `gs_migrations` (an audit log) and `gs_vehicle_access` (persistent shared keys). The in-game admin suite adds `gs_garages` (dynamic garages) and `gs_settings` (live config overrides) as needed.

{% hint style="info" %}
Start your framework at least once before GS Garages so the framework's own vehicle table exists.
{% endhint %}

---

## Step 4: Admin Permissions

The in-game admin panel and the server command accept the `goonsquad.admin` ACE (the server console always has access):

```cfg
add_ace group.admin goonsquad.admin allow
```

To grant a specific principal:

```cfg
add_ace identifier.license:YOUR_LICENSE goonsquad.admin allow
```

Administrators can create and edit garages live, change nearly every config value at runtime, force garages open, and impound vehicles. Grant this permission only to trusted staff.

---

## Step 5: Review Configuration

At minimum, review:

```lua
Config.Framework = 'auto'
Config.Fuel = 'auto'
Config.Keys = 'auto'
Config.Target = 'markers'
Config.Notify = 'ui'

Config.DefaultGarage = 'legion_public'
Config.DefaultImpound = 'impound_lot'
Config.Fees = { retrieve = 0, impound = 500, transfer = 150 }
```

Then review the garage list in `config/garages.lua` — thirteen example garages ship (six public lots, three job garages, one gang garage, one house garage, and two impound lots). Replace or edit them to match your map.

See [Configuration](configuration.md) for the full guide.

---

## Step 6: Import Existing Vehicles (Optional)

If your server already has owned vehicles, run the native import once to tidy up rows that dealerships left incomplete (missing garage, state, condition, or category):

```text
gs_garages import            # dry run — prints what WOULD change
gs_garages import --apply    # actually writes it
```

Migrating from another garage script? See [Migrating from Another Garage Resource](administration.md#migrating-from-another-garage-resource).

{% hint style="warning" %}
Back up the database before applying any import. The dry run is free — always read it first.
{% endhint %}

---

## Step 7: Verify

1. Join the server.
2. Run `gs_garages status` from the server console and confirm the framework, key provider, and column map are correct.
3. Walk to a shipped garage (Legion Square Parking is at the default spawn area) and open it with the prompt, target zone, or `/garage`.
4. Buy or register a vehicle, retrieve it, and drive to a garage bay — the `E — PARK` prompt should appear.
5. Test an impound: `gs_garages impound <plate> 500`, then collect it from the Davis impound lot.
