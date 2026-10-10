# Integrations

## Framework and Inventory

Automatic framework order is Qbox (`qbx_core`), QBCore (`qb-core`), ESX (`es_extended`), then standalone. Explicit values are `qbox`, `qb`, `esx` and `standalone`. Qbox uses its exports and can use the compatibility player object where available. Character identifiers are citizen IDs on QB/Qbox, ESX identifiers on ESX and the available license-style identifier on standalone.

Inventory detection checks started resources in this order: `ox_inventory`, `qb-inventory`, `qs-inventory`, `lj-inventory` and `esx_inventoryhud`, otherwise `none`. Item operations can fall back to the framework player inventory. ESX inventoryhud provides image detection while ESX player APIs handle item operations. There is no dedicated `ps-inventory` adapter; use a compatible framework API or adapt the bridge and image path.

The open `shared/bridge.lua` contains player/name/gender/identifier, money, item capacity/add/remove/usable registration and item label/image adapters. Configure only adapters that match the versions running on your server. Automatic resource detection alone does not prove compatibility.

## Standalone Hooks

Supply `Config.Standalone.addMoney(src, account, amount, reason)`, `addItem(src, item, amount, metadata)`, `removeItem(src, item, amount)` and `canCarry(src, item, amount)` as needed. Implement real operations and return true on success and false on failure. Money and item delivery fail when no adapter exists. Standalone does not simulate successful payouts or provide an internal wallet/bag.

Configuration is shared. If a hook invokes server-only APIs, guard it with `IsDuplicityVersion()` or implement it inside the server branch of the open bridge. Usable-item callbacks must match your inventory lifecycle; see [Installation](installation.md).

## Garage, Keys and Fuel

Garage resource names are configured in `Config.StarterVehicle.garageResources`. The first started name satisfies the default availability requirement. Ownership inserts use `player_vehicles` for QB/Qbox, `owned_vehicles` for ESX and `gs_fh_vehicles` for standalone. The bridge also exposes plate checks, stored-state updates and `SaveVehicleProps` for adapting custom schemas without opening claim logic.

Set `garageName` to a real identifier in your garage. Match its ownership columns, stored-state semantics and JSON format. `requireGarage = false` removes availability detection but does not implement storage or retrieval.

Key detection uses the first running configured resource. Included branches cover qbx_vehiclekeys, qb-vehiclekeys, wasabi_carlock, qs-vehiclekeys, MrNewbVehicleKeys, Renewed-Vehiclekeys, mk_vehiclekeys, cd_garage, t1ger_keys and vehicles_keys. Fuel uses the native level plus detected ox_fuel, LegacyFuel, cdn-fuel, ps-fuel or lc_fuel adapters. Test keys and fuel with your exact versions. Client properties use framework helpers or optional ox_lib, with model/plate fallback; the server replaces authoritative model/plate and strips selected performance upgrades before saving.

## Discord and Notifications

Role gating requires a configured guild, allowed role IDs, player Discord identifier and a bot with access to guild member information. Keep the token in the private server file or convar. Configure roles on the server, not in the UI. Default notification order is ox_lib, QB/Qbox, ESX, then themed NUI toasts.

Claims and optional community-chat mirroring use separate webhook secrets. Chat mirror mentions are neutralized. Private DMs are not mirrored. URLs must use HTTPS; blank URLs send nothing.

## Other Resources

Use the public exports to open the hub from a radial menu or award season XP from approved server activities. Events beginning `gs-baddiehub:server:` are internal transport and item/property handling, not an integration API. See [API & Exports](exports.md).
