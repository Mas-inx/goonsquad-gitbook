# GS Garages

A drag-and-drop, server-authoritative garage system for FiveM with an AAA racing-game interface, a live 3D vehicle preview, in-world park and open prompts, impound lots, job and gang fleets, automatic housing integration, and a full in-game admin suite. It runs on a clean ESX, QBCore, Qbox, or standalone server with no edits to any framework file.

**Version:** 1.0.0 | **Price:** $19.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7641428)

**Open Source:** $44.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7641437)

**Product page:** [goonsquadstudios.com/garages](https://goonsquadstudios.com/garages) — full feature breakdown, FAQ and guides

{% embed url="https://www.youtube.com/watch?v=9GhWrYPadbE" %}

---

## How It Works

GS Garages reads the framework's **own owned-vehicle table** (`player_vehicles` on QBCore/Qbox, `owned_vehicles` on ESX) as the single source of truth. Any dealership or script that writes that table is automatically compatible — a car sold thirty seconds ago shows up on the next garage open with no integration work.

Everything trust-sensitive is decided on the server: ownership, money, state transitions, impound release, and rate limiting. The client asks; the server decides. Plates are read off the vehicle entity server-side, never taken from the client's message.

---

## Features

### Garages
- Public, house, job, gang, and impound garage types, all defined as data in `config/garages.lua`
- Per-garage vehicle categories, capacity limits, fees, blips, and interaction style
- Job and gang garages with grade-gated access and optional shared fleets nobody owns
- Two impound lots shipped, with job-based release permissions and depot pricing
- Garages far from the player cost nothing — nothing runs per-frame while the UI is closed

### Interface
- AAA racing-game front end: letterboxed live preview, condensed display typography, one volt accent
- Live 3D preview of the selected vehicle on a turntable with composed camera presets, drag-to-orbit, zoom, and a cinematic mode
- In-world DUI nameplate standing behind the previewed car — real world geometry, occluded by the vehicle
- Performance bars read from the model's real handling data
- Search, category filters, favorites, nicknames, and condition readouts on every vehicle
- Procedurally synthesized UI sounds — no audio files

### Vehicle Management
- Retrieve, park, transfer between garages, and impound release, all server-validated
- World-anchored `E — PARK` prompt at the garage while driving — parking never opens a menu
- World-anchored `E — OPEN GARAGE` prompt on foot, alongside target zones or markers
- Favorites, nicknames, "find my car" waypoints, and odometer mileage tracking
- Give temporary keys to a nearby player, with optional persistent shared access
- Duplicate-spawn protection: a retrieve flips state server-side first, and unconfirmed spawns roll back with a refund

### Integrations
- Auto-detected framework: Qbox, QBCore, ESX, or standalone
- Auto-detected fuel, vehicle key, and target resources, each with an override hook for anything unsupported
- Automatic house garages from ps-housing, qbx_properties, qs-housing, qb-houses, loaf_housing, esx_property, and rtx_housing — plus a custom provider hook
- Cross-script importers for qb-garages, jg-advancedgarages, cd_garage, and loaf_garage

### Administration
- Full in-game admin panel: create, edit, disable, and restore garages by aiming placement tools in the world — no restart
- Nearly every config option editable live from the Settings tab, persisted to the database and synced to every client
- Idempotent, additive database migrations that never drop or rename a column
- Dry-run imports that print exactly what would change before anything is written
- Batched Discord webhook logging

---

## Framework Support

| Framework | Support | Vehicle table |
|---|---|---|
| Qbox (`qbx_core`) | Full | `player_vehicles` (old QB shape and newer `qbx_vehicles` shape both handled) |
| QBCore (`qb-core`) | Full | `player_vehicles` |
| ESX Legacy (`es_extended`) | Full | `owned_vehicles` (ESX's `stored` column is kept in sync) |
| Standalone | Full | `gs_vehicles` (created automatically) |

Detection runs in the order Qbox → QBCore → ESX → standalone. Qbox is checked first because some installs ship a `qb-core` compatibility shim.

---

## Requirements

| Dependency | Required | Purpose |
|---|---|---|
| [ox_lib](https://github.com/overextended/ox_lib) | Yes | Vehicle properties, callbacks, text UI |
| [oxmysql](https://github.com/overextended/oxmysql) | Yes | All database access |
| Framework | Optional | Qbox, QBCore, or ESX; standalone mode works without one |
| Fuel resource | Optional | LegacyFuel, ox_fuel, cdn-fuel, ps-fuel, ND_fuel, hyon_fuel, Renewed-Fuel; native GTA fuel is the fallback |
| Key resource | Optional | qbx_vehiclekeys, qb-vehiclekeys, wasabi_carlock, qs-vehiclekeys, MrNewbVehicleKeys, cd_garage |
| Target resource | Optional | ox_target or qb-target; a marker fallback is included |
| Housing resource | Optional | Automatic per-house garages when a supported script is running |

Every optional integration is detected at runtime and degrades cleanly when absent.

---

## Quick Start

1. Follow the [Installation](installation.md) guide.
2. Review the [Configuration](configuration.md), especially the garage list, fees, and feature switches.
3. Start the server and run `gs_garages status` to confirm detection.
4. Use `/garage` near a shipped garage, or drive up and press `E` at a bay to park.
5. Read the [Usage](usage.md) guide for player workflows and [Administration](administration.md) for the in-game admin suite.
