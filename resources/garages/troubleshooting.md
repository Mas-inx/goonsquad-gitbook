# Troubleshooting

Enable `Config.Debug = true` while reproducing a problem, then capture the complete `[gs-garages]` lines from the server console and relevant F8 console output.

Run this first from the server console:

```text
gs_garages status
```

It reports the detected framework, key provider, garage count, schema readiness, and the exact table and column map the resource resolved.

---

## Startup and Database

### `table 'player_vehicles' does not exist`

Start your framework once so it creates its own schema, then restart GS Garages. The resource reads the framework's vehicle table — it does not create it (except in standalone mode, where `gs_vehicles` is created automatically).

### `could not read INFORMATION_SCHEMA`

The MySQL user in your connection string needs `SELECT` on `INFORMATION_SCHEMA` and `ALTER` on the vehicle table. Migrations check the schema before adding any column.

### `has no usable state column`

`Config.Migration.runOnStart` is off and the column was never added. Turn it on, or run the migration once:

```text
gs_garages migrate
```

### Wrong framework detected

1. Make sure the framework starts before GS Garages.
2. Run `gs_garages status` and read the detected framework.
3. Force it if needed: `Config.Framework = 'qbox' | 'qbcore' | 'esx' | 'standalone'`.

Detection order is Qbox → QBCore → ESX → standalone; some Qbox installs ship a `qb-core` shim, which is why Qbox is probed first.

---

## Vehicles and Ownership

### Garage opens but the vehicle list is empty

1. Confirm `gs_garages status` prints the right framework and vehicle table.
2. Confirm the owner column matches what your dealership writes — the resource matches on `citizenid` (QBCore/Qbox) or `identifier` (ESX).
3. Check the vehicle's `garage` column: a vehicle parked at garage id that no longer exists follows the unknown-garage routing (`Config.DefaultGarage` / `Config.AnyGarageIfUnknown`).

### The same vehicle appears in every public garage

That vehicle's `garage` column is NULL or points at an unknown id, and `Config.AnyGarageIfUnknown = true` makes such vehicles retrievable from any public lot. Park it once to settle it, run `gs_garages import --apply` to backfill all such rows, or set `AnyGarageIfUnknown = false`.

### A vehicle is stuck marked "out" but does not exist

It will self-heal: a row marked out with no matching entity anywhere on the server is allowed to respawn on the next retrieve. If the client crashes during a spawn, the state also rolls back automatically after 20 seconds with the fee refunded.

### Keys are not handed over on retrieval

`gs_garages status` prints the detected key provider. If your key script renamed its export in a newer version, the adapter warns once on the console — fix the export name in `bridge/keys.lua` (it ships open), or route it through `Config.GiveKeys`.

---

## Interface and Interaction

### Blank interface on open

The NUI build is missing or stale. The shipped `web/build/` must be present in the resource folder — if you modified the UI source, run `npm run build` inside `web/` and restart.

### No prompt, marker, or target zone at a garage

1. Check `Config.Target` and whether the target resource starts before GS Garages.
2. Remember a garage can force `interaction.type = 'marker'` for itself.
3. The open prompt only shows on foot within `Config.OpenPrompt.showDistance`; the target/marker only activates within the interaction radius.

### The park prompt does not appear

The prompt only shows while **driving** a vehicle whose category the garage accepts, within `Config.ParkPrompt.showDistance` of a spawn bay. Check the garage's `categories` list against the vehicle. Pressing the key only works inside `Config.StoreRadius` — the prompt's ready state shows when you are close enough.

### "This garage is full"

The garage has a `capacity` and the owner already has that many vehicles stored there. Capacity is counted per owner across stored vehicles in that garage.

---

## Configuration

### A config file edit has no effect

The same option probably has a live override from the in-game admin panel — the `gs_settings` row wins on every boot. Open `/garageadmin` → Settings, find the option marked with a dot, and reset it to hand control back to the file.

### Admin panel will not open

The player needs the `goonsquad.admin` ACE (or `command.gs_garages`), and `Config.Admin.enabled` must be true:

```cfg
add_ace group.admin goonsquad.admin allow
```

---

## Housing

### House garages do not appear

1. Confirm the housing script is detected: force it with `Config.Housing.provider` if needed.
2. qb-houses: the house needs `/setgarage` used on it once. esx_property: an admin must set the garage position in game. qbx_properties: its optional `property_garages.sql` migration must be imported.
3. Purchases and key changes on scripts without server events land within the re-scan interval (`Config.Housing.refresh`, default 300 seconds). Another resource can force it instantly with `exports['gs-garages']:RefreshHouseGarages()`.

---

## Still Stuck?

Open a ticket in the [Goonsquad Discord](../../support.md) with:

- The full `gs_garages status` output
- The `[gs-garages]` console lines with `Config.Debug = true`
- Your framework and the housing/key/fuel/target resources involved
